---
layout: post
title: IGME 470 - Musical Gloves
---

Last week marked the end of Project 2 in my Alternative Interfaces and Physical Computing course. For our second project we had to create a wearable interactive experience for users. I decided that I wanted to try to create an instrument!

My thought was that I can connect 5 force sensative resistors to a glove, reading an analog input from each one. Then, representing the 5 fingers as a 5 digit binary number, N. N, now represents the number of halfsteps above the root note the instrument is tuned (or programmed) to using an approximation of the traditional 12 intervals used in western music theory. I use an equal temperment system, a compromise of a just temperment system where each of the 12 tones within one octave can be found by multiplying the closest member of the same harmonic series with the ratio of two whole numbers. The difference between an equal temperment system and a just temperment system is that a just temperment system will vary the interval constants with the root frequency the instrument is tuned to. Equal temperment, on the other hand, approximates these constants as 12 uniform slices of the square root of 2- this way they translate between different root frequencies better. This allows you to play in any key you desire as well or as poorly as you can manage. Unfortunately because of the imprecision of choosing a root frequency (a problem I am still trying to deal with), this means it will be very very difficult to play along with any other instrument or song (as the key will probably never even be close to matching).

The interval constants I chose to use can be found via the following link: [phy.mtu.edu/~suits/scales.html](http://phy.mtu.edu/~suits/scales.html).

For this project I utilised the PIC16F1829, which has been really growing on me throughout this year. The controller has a boatload of pins which support ADC, aswell as PWM capabilities. I strive to try and maintain a 50% duty cycle ratio for a nice full sound, and found that I can approximately generate notes from ~290 Hz to ~4200 Hz. This range can be dynamically adjusted by intelligently setting the prescaler of the timer I am using for my PWM output. I also include a potentiometer for adjusting volume (Unfortunately the neighbors did not seem to like the lulling cry of square waves through my thin Riverknoll walls- the small price to pay for science).

At the following link you can find a video of the glove in action: [Video Link](https://drive.google.com/file/d/0B2ou4k3PM0y4dDFzWUpuMVhSRVp3cC1MQjFMSUE0eHRsNGhr/view?usp=sharing).


And below you can find the code for the project (Released under QPL if anybody wants to use it):


	
	/* 
	 * File:   1829Instrument.c
	 * Author: Nicholas Gallagher
	 *
	 * Created on January 28, 2016, 2:43 PM
	 */
	
	#include <stdio.h>
	#include <stdlib.h>
	
	
	#include <htc.h>            //PIC hardware mapping
	
	//Set configuration bits
	__CONFIG(FOSC_INTOSC & WDTE_OFF & PWRTE_OFF & MCLRE_OFF & CP_OFF & CPD_OFF & BOREN_ON & CLKOUTEN_OFF & IESO_OFF & FCMEN_OFF);
	__CONFIG(WRT_OFF & PLLEN_OFF & STVREN_OFF & LVP_OFF);
	
	#define _XTAL_FREQ 31000
	
	#define FOSC 31000
	
	#define DUTYCYCLEMSBPIN CCPR1L
	#define DUTYCYCLELSBPIN CCP1CONbits.DC1B
	
	#define SAMPLEPERIODPIN PR2
	
	#define SPEAKERPOWERPIN LATCbits.LATC0
	
	#define DUTYCYCLEMSB 0b00001111
	#define DUTYCYCLELSB 0b01
	#define SAMPLEPERIOD 0b00011100
	
	#define FSR1 0b00010
	#define FSR2 0b00100
	#define FSR3 0b00101
	#define FSR4 0b00110
	#define FSR5 0b01010

	#define PRESSURETHRESHOLD 128



	const int DUTYCYCLE = ((DUTYCYCLEMSB << 2) + DUTYCYCLELSB);

	const float PULSEWIDTH = (float)DUTYCYCLE / (float)FOSC;

	const float NUMERATOR = (float)((SAMPLEPERIOD + 1) * 4);
	const float DENOMINATOR = (float)FOSC;

	const float PWMPERIOD = (const int)(NUMERATOR/DENOMINATOR);

	const float INTERVALS[] = { 1.0f, 1.05946f, 1.12246f, 1.18921f, 1.25992f, 1.33483f, 1.41421f, 1.49831f, 1.58740f, 1.68179f, 1.78180f, 1.88775f, 2.0f};

	//Initialize internal timer for 32MHz freq. selection
	void InitializeOscillator(void)
	{
	    OSCCONbits.SCS = 0b10;
	    OSCCONbits.IRCF = 0b0000;   //31khz HLINTOSC set
	    OSCCONbits.SPLLEN = 0b0;    // no 4xPLL
	                                //31 Khz FOSC speed!
	}

	void InitializePWM(void)
	{
	    TRISCbits.TRISC5 = 1;
	    SAMPLEPERIODPIN = SAMPLEPERIOD;          //Period
	    CCP1CON = 0b00001100;
	    DUTYCYCLEMSBPIN = DUTYCYCLEMSB;        //Duty cycle
	    DUTYCYCLELSBPIN = DUTYCYCLELSB;
	
	    CCPTMRSbits.C2TSEL = 0b00;      //Set Timer2 to use with PWM
	    PIR2bits.C2IF = 0b0;          //Clear timer 2 interrupt flag
	    T2CONbits.T2CKPS = 0b01;         //1:4 prescaler for timer2
	    T2CONbits.TMR2ON = 0b1;         //Turn timer 2 on
	    //T2CON = 0b00000110;
	    TRISCbits.TRISC5 = 0;
	}

	void InitializeADC(void)
	{
	    ANSELAbits.ANSA2 = 1;   //FSR1
	    ANSELCbits.ANSC0 = 1;   //FSR2
	    ANSELCbits.ANSC1 = 1;   //FSR3
	    ANSELCbits.ANSC2 = 1;   //FSR4
	    ANSELBbits.ANSB4 = 1;   //FSR5
	    
	    //Set ADC conversion to use internal oscillator 0bx11
	    ADCON1bits.ADCS = 0b111;
	    //Set the negative voltage reference to VSS
	    ADCON1bits.ADNREF = 0;
	    //Set the positive voltage reference to VDD
	    ADCON1bits.ADPREF = 0b00;
	    //Left justify the results (ADRESH holds 8 MSB, ADRESL holds 2 LSB in the MSB)
	    //See page 147 of data sheet for clarification
	    ADCON1bits.ADFM = 0;
	    
	    //Select the ADC input channel (AN2 == RA2)
	    ADCON0bits.CHS = FSR1;
	    
	    //Turn on ADC
	    ADCON0bits.ADON = 1;
	    
	}
	
	void InitializePins()
	{
	    TRISCbits.TRISC5 = 0;   //Speaker / transistor switch
	    TRISAbits.TRISA2 = 1;   //FSR1
	    TRISCbits.TRISC0 = 1;   //FSR2
	    TRISCbits.TRISC1 = 1;   //FSR3
	    TRISCbits.TRISC2 = 1;   //FSR4
	    TRISBbits.TRISB4 = 1;   //FSR5
	    
	    
	    LATC = 0;
	}
	
	
	void FreqOut(int frequency)
	{   
	    int samplerVal = ((float)FOSC / (4.0f * (float)frequency)) - 1.0f;
	    SAMPLEPERIODPIN = (char)samplerVal;
	    samplerVal /= 2;
	    DUTYCYCLELSBPIN = (samplerVal & 0b11);
	    DUTYCYCLEMSBPIN = (samplerVal >> 2);
	}
	
	unsigned char ReadPWM(unsigned char channel)
	{
	    ADCON0bits.CHS = channel & 0b00011111;
	    __delay_us(5);      //ADC charging cap takes 5us to settle
	    GO = 1;             //Begin ADC process
	    while(GO) continue; //GO bit will be set low once ADC process completes
	    return ADRESH;      //return 8 MSB of the result
	}
	
	/*
	 * 
	 */
	int main(int argc, char** argv) 
	{
	    InitializePWM();
	    InitializePins();
	    InitializeADC();
	
	    int freq = 1;
	    int limit = 220;
	    
	    while(1)
	    {
	        char byte = 0;
	        byte |= ((ReadPWM(FSR1) > PRESSURETHRESHOLD));
	        byte |= ((ReadPWM(FSR2) > PRESSURETHRESHOLD) << 1);
	        byte |= ((ReadPWM(FSR3) > PRESSURETHRESHOLD) << 2);
	        byte |= ((ReadPWM(FSR4) > PRESSURETHRESHOLD) << 3);
        	byte |= ((ReadPWM(FSR5) > PRESSURETHRESHOLD) << 4);
	        
	        int octave = (byte - 1) / 12;
	        int note = ((byte - 1) % 12); 
	        
	        switch(byte)
	        {
	            case 0:
	                SPEAKERPOWERPIN = 0;
	                DUTYCYCLEMSBPIN = 0;
	                DUTYCYCLELSBPIN = 0;
	            break;
	            default:
	                FreqOut((31 << octave) * INTERVALS[note]);
	                break;
	        }
	    }
	    return (EXIT_SUCCESS);
	}
	
