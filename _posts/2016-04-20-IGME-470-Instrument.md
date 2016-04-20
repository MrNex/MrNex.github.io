---
layout: post
title: IGME-470 Instrument
---

As I believe I mentioned in here, the last project we got assigned in my Physical Computing and Alternative Interfaces class was to do something involving audio. I wanted to continue the instrument I was building onto a pair of gloves (without the fussyness of trying to put it on a glove). So I did! The instrument is played with 10 force sensitive resistors, which you press using your fingers. Each hand controls a set of 32 distinct tones, allowing you to play any two tones simultaneously for a total of 1024 combinations. One set of FSRs produces tones which are an octave higher than the other. The instrument was originally created with playing walking basslines with complex melodies over it. The way this is done, is by having the FSRs act as a 5 digit binary number, which is the number of semitones in an equal tempered scale above a root frequency. This means that not only will putting down a finger result in a change in tone, but lifting up a finger will also change the tone leading to interesting and fast paced patterns. Further more, it allows the artist to jump around between octaves with ease (simply by placing down the pinky will jump 16 "keys" or tones)- a style which I think is demonstrated well through one of [Bobby Mcferrin's Performances](https://www.youtube.com/watch?v=81uJZIF9TCs).

The tones are generated through two separate PWM outputs, which are then added together through a summation circuit. The waveforms then use a transistor to pull a more powerful current with a voltage of 9 volts, in the same waveform. I then send this through a lo pass filter to help smooth the square waves, resulting in triangle waves. These then drive the speaker at the desired frequency.

Below you can see the circuit:
<figure>
<a href="/images/2016-04-20-InstrumentCircuit.png">
<img src="/images/2016-04-20-InstrumentCircuit.png">
</a>
<figcaption>It should be noted that missing from the image here are the 10 force sensitive resistors all wired to separate analog capable pins on my microcontroller.
</figure>

I had a lot of fun with this assignment. Eventually I stopped working on it because every time I took it out I would just end up playing songs that I could translate from my background playing piano or my small knowledge of music theory. Some features I did include that I was very happy with is the ability to tune the instrument to both the chromatic or harmonic scales (and the code for pentatonic, or similar, would be trivial). This allows the instrument to be given to somebody with no musical background and them easily be able to play in tune. While others, who understand the strange combination of music theory / temperment and binary needed to play the instrument properly, can perform more elaborate pieces including dissonance, key changes, and more.

Here is the code for a PIC16F1829 microcontroller to drive the circuit above:

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
	
	///
	//Treble
	//Treble duty cycle
	#define TREBLEDUTYCYCLEMSBPIN CCPR1L
	#define TREBLEDUTYCYCLELSBPIN CCP1CONbits.DC1B
	//Treble sample period
	#define TREBLESAMPLEPERIODPIN PR2
	
	
	///
	//Bass
	//Bass duty cycle
	#define BASSDUTYCYCLEMSBPIN CCPR2L
	#define BASSDUTYCYCLELSBPIN CCP2CONbits.DC2B
	//Bass sample period
	#define BASSSAMPLEPERIODPIN PR4
	
	
	#define FSR1 0b00010
	#define FSR2 0b00100
	#define FSR3 0b00101
	#define FSR4 0b00110
	#define FSR5 0b01010
	
	#define FSR6 0b01011
	#define FSR7 0b00011
	#define FSR8 0b00111
	#define FSR9 0b01000
	#define FSR10 0b01001
	
	
	#define PRESSURETHRESHOLD 128
	
	#define BASSRANGE 5
	
	#define OCTAVE(x,y) ((x)/(y))
	#define NOTE(x,y) ((x)%(y))
	
	#define CHROMATICOCTAVE(x) OCTAVE((x), 12)
	#define HARMONICOCTAVE(x) OCTAVE((x),7)
	
	#define CHROMATICNOTE(x) NOTE((x),12);
	#define HARMONICNOTE(x) HARMONICS[NOTE((x),7)]
	
	#define ROOTFREQUENCY 31
	#define Freq(octave, note) ((ROOTFREQUENCY << (octave)) * INTERVALS[(note)]);
	
	#define SLEEPTIME 10000
	
	int timeSinceInput = 0;
	int cyclesSinceTime = 0;
	
	const float INTERVALS[] = { 1.0f, 1.05946f, 1.12246f, 1.18921f, 1.25992f, 1.33483f, 1.41421f, 1.49831f, 1.58740f, 1.68179f, 1.78180f, 1.88775f, 2.0f};
	const int HARMONICS[] = { 0, 2, 4, 5, 7, 9, 11};
	
	const int numNotes = 30;
	const int ODETOJOYTREBLENOTES[] = { 2, 2, 3, 4, 4, 3, 2, 1, 0, 0, 1, 2, 2, 1, 1, 2, 2, 3, 4, 4, 3, 2, 1, 0, 0, 1, 2, 1, 0, 0 };
	const int ODETOJOYTREBLETIMES[] = { 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 8, 2, 16, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 4, 8, 2, 16 };
	
	const int ODETOJOYBASSNOTES[] = {2, 4, 0};
	const int ODETOJOYBASSQUEUES[] = {0, 5, 10, 15, 20, 25};
	
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
		//Initializing Treble PWM
		TRISCbits.TRISC5 = 1;
		TREBLESAMPLEPERIODPIN = 0;          //Period
		CCP1CON = 0b00001100;
		TREBLEDUTYCYCLEMSBPIN = 0;        //Duty cycle
		TREBLEDUTYCYCLELSBPIN = 0;
	
		CCPTMRSbits.C1TSEL = 0b00;      //Set Timer2 to use with PWM 1 (treble)
		PIR2bits.C2IF = 0b0;          //Clear timer 2 interrupt flag
		T2CONbits.T2CKPS = 0b01;         //1:16 prescaler for timer2
		T2CONbits.TMR2ON = 0b1;         //Turn timer 2 on
		TRISCbits.TRISC5 = 0;
	
	
		//Initializing Bass PWM
		TRISAbits.TRISA5 = 1;
	
		//Route output of CPP2 register to RA5
		APFCON1bits.CCP2SEL = 1;
	
		BASSSAMPLEPERIODPIN = 0;
	
		CCP2CON = 0b00001100;           //Configures CCP2 for PWM
	
		BASSDUTYCYCLEMSBPIN = 0;
		BASSDUTYCYCLELSBPIN = 0;
	
		CCPTMRSbits.C2TSEL = 0b01;       //Set Timer4 to use with PWM2 (bass)
		PIR3bits.TMR4IF = 0;
		T4CONbits.T4CKPS = 0b01;        //1:4 prescaler on timer 4
		T4CONbits.TMR4ON = 0b1;            //Turn timer 4 on
	
		TRISAbits.TRISA5 = 0;
	
	}
	
	void InitializeADC(void)
	{
		ANSELAbits.ANSA2 = 1;   //FSR1
		ANSELCbits.ANSC0 = 1;   //FSR2
		ANSELCbits.ANSC1 = 1;   //FSR3
		ANSELCbits.ANSC2 = 1;   //FSR4
		ANSELBbits.ANSB4 = 1;   //FSR5
	
		ANSELBbits.ANSB5 = 1;   //FSR6
		ANSELAbits.ANSA4 = 1;   //FSR7
		ANSELCbits.ANSC3 = 1;   //FSR8
		ANSELCbits.ANSC6 = 1;   //FSR9
		ANSELCbits.ANSC7 = 1;   //FSR10
	
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
		TRISCbits.TRISC5 = 0;   //Treble Speaker / transistor switch
		TRISAbits.TRISA5 = 0;
		TRISAbits.TRISA2 = 1;   //FSR1
		TRISCbits.TRISC0 = 1;   //FSR2
		TRISCbits.TRISC1 = 1;   //FSR3
		TRISCbits.TRISC2 = 1;   //FSR4
		TRISBbits.TRISB4 = 1;   //FSR5
	
		TRISBbits.TRISB5 = 1;   //FSR6
		TRISAbits.TRISA4 = 1;   //FSR7
		TRISCbits.TRISC3 = 1;   //FSR8
		TRISCbits.TRISC6 = 1;   //FSR9
		TRISCbits.TRISC7 = 1;   //FSR10
	
	
		LATC = 0;
		LATA = 0;
	
	}
	
	
	void TrebleFreqOut(int frequency)
	{  
		int samplerVal = (int)((float)FOSC / (4.0f * (float)frequency)) - 1;
		TREBLESAMPLEPERIODPIN = (char)samplerVal;
		samplerVal /= 2;
		TREBLEDUTYCYCLELSBPIN = (samplerVal & 0b11);
		TREBLEDUTYCYCLEMSBPIN = (samplerVal >> 2);
	}
	
	void BassFreqOut(int frequency)
	{
		int samplerVal = (int)((float)FOSC / (4.0f * (float)frequency)) - 1;
		BASSSAMPLEPERIODPIN = (char)samplerVal;
		samplerVal /= 2;
		BASSDUTYCYCLELSBPIN = (samplerVal & 0b11);
		BASSDUTYCYCLEMSBPIN = (samplerVal >> 2);
	
	}
	
	unsigned char ReadPWM(unsigned char channel)
	{
		ADCON0bits.CHS = channel & 0b00011111;
		__delay_us(5);      //ADC charging cap takes 5us to settle
		GO = 1;             //Begin ADC process
		while(GO) continue; //GO bit will be set low once ADC process completes
		return ADRESH;      //return 8 MSB of the result
	}
	
	void PlaySong(void)
	{
		int lastBass = 0;
		for(int i = 0; i < numNotes; i++)
		{
			int trebleOctave = 2;
			int trebleNote = HARMONICNOTE(ODETOJOYTREBLENOTES[i]);
	
			int freq = (int)Freq(trebleOctave, trebleNote);
	
			if(freq > 0)
			{
				TrebleFreqOut(freq);
			}
			if(i % 3 == 0)
			{
				int bassOctave = 0;
				int bassNote = HARMONICNOTE(ODETOJOYBASSNOTES[lastBass]);
				freq = (int)Freq(bassOctave, bassNote);
				BassFreqOut(freq);
				lastBass++;
				if(lastBass > 2) lastBass = 0;
			}
	
			for(int j = 0; j < ODETOJOYTREBLETIMES[i]; j++)
			{
				__delay_ms(500);  
			}
			TREBLEDUTYCYCLEMSBPIN = 0;
			TREBLEDUTYCYCLELSBPIN = 0;
			//BASSDUTYCYCLEMSBPIN = 0;
			//BASSDUTYCYCLELSBPIN = 0;
			__delay_ms(500);
		}
	
		TREBLEDUTYCYCLEMSBPIN = 0;
		TREBLEDUTYCYCLELSBPIN = 0;
		BASSDUTYCYCLEMSBPIN = 0;
		BASSDUTYCYCLELSBPIN = 0;
	
	}
	
	/*
	*
	*/
	int main(int argc, char** argv)
	{
		InitializePWM();
		InitializePins();
		InitializeADC();
	
		PlaySong();
	
		while(1)
		{
			char bassByte = 0;
			char trebleByte = 0;
	
	
			bassByte |= ((ReadPWM(FSR1) > PRESSURETHRESHOLD) << 0);
			bassByte |= ((ReadPWM(FSR2) > PRESSURETHRESHOLD) << 1);
			bassByte |= ((ReadPWM(FSR3) > PRESSURETHRESHOLD) << 2);
			bassByte |= ((ReadPWM(FSR4) > PRESSURETHRESHOLD) << 3);
			bassByte |= ((ReadPWM(FSR5) > PRESSURETHRESHOLD) << 4);
	
			trebleByte |= ((ReadPWM(FSR6) > PRESSURETHRESHOLD) << 0);
			trebleByte |= ((ReadPWM(FSR7) > PRESSURETHRESHOLD) << 1);
			trebleByte |= ((ReadPWM(FSR8) > PRESSURETHRESHOLD) << 2);
			trebleByte |= ((ReadPWM(FSR9) > PRESSURETHRESHOLD) << 3);
			trebleByte |= ((ReadPWM(FSR10) > PRESSURETHRESHOLD) << 4);
	
			int bassOctave = HARMONICOCTAVE(bassByte);
			int trebleOctave = HARMONICOCTAVE(trebleByte) + 1;
			int bassNote = HARMONICNOTE(bassByte);
			int trebleNote = HARMONICNOTE(trebleByte);
	
			//int bassOctave = CHROMATICOCTAVE(bassByte);
			//int trebleOctave = CHROMATICOCTAVE(trebleByte) + 1;
			//int bassNote = CHROMATICNOTE(bassByte);
			//int trebleNote = CHROMATICNOTE(trebleByte);       
	
			if(bassByte == 0)
			{
				BASSDUTYCYCLEMSBPIN = 0;
				BASSDUTYCYCLELSBPIN = 0;
	
				if(trebleByte == 0)
				{
					timeSinceInput++;
					if(timeSinceInput >= SLEEPTIME)
					{
						timeSinceInput = 0;
						PlaySong();
					}              
				}
				else
				{
					timeSinceInput = 0;
	
				}
			}
			else
			{
				int bassFreq = (int)Freq(bassOctave, bassNote);
				BassFreqOut(bassFreq);
			}
	
			if(trebleByte == 0)
			{
				TREBLEDUTYCYCLEMSBPIN = 0;
				TREBLEDUTYCYCLELSBPIN = 0;
			}
			else
			{
				int trebleFreq = (int)Freq(trebleOctave, trebleNote);
				TrebleFreqOut(trebleFreq);
			}
		}
		return (EXIT_SUCCESS);
	}
