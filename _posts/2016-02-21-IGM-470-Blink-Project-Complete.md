---
layout: post
title: IGME-470 Blink - Project Completed
---

Recently I posted about a project proposal in one of my courses, Physical Computing and Alternative Interfaces. We had to create a project which used LEDs in some fashion. I decided to make a Simon Says game.

It game as no surprise most of my time was spent wiring and re-wiring, the actual programming was done relatively early. One thing that tripped me up a little more than expected was creating my own random number generator, and theres a lot of improvement which could be made. I completed mine by simulating a linear feedback shift register where the seed was based off of a timer value when recieving user input. The trouble is that the timer is set to update once per instruction cycle, which means that while I'm waiting for input the clock is incrementing by a fixed amount each time the loop executes to check the status of a switch. Unfortunately this lead to all seeds being a multiple of a number-- and with only 8 bits & 3 possible outcomes to work with this causes extremely similar "random" patterns to be generated every single time. This could be fixed in the future by altering the clock register so that it is incremented based off of elapsed time rather than instruction cycles. Alternatively, allowing myself a larger amount of memory with which to randomize the bits while generating a random number could have also solved this problem. It is possible that just having more outcomes rather than only 3 possible LEDs could have given the perception of it being more random than it is.

Here is a picture of the completed circuit:
<figure>
<a href="/images/2016-02-21-IGME-470-BlinkComplete.jpg"><img src="/images/2016-02-21-IGME-470-BlinkComplete.jpg"></a>
<figcaption>The completed Simon Says circuit</figcaption>
</figure>


I took a video earlier, but it seems to have become corrupted.

The behavior of the game is as follows:

When switched on the game begins, first the user will be shown a pattern of 5 lights. Each light has a button in front of it that the user must press in the order the sequence was presented. If the user finishes the sequence correctly, the lights will flash from right to left 5 times in a quick succession. At this point the game begins again adding one random element to the existing sequence. If, however, the user enters the sequence incorrectly, upon the first incorrect key being pressed all lights will light up simultaneously for 3 long blinks- inturrupting the user. The sequence will then play again (but almost imperceptibly faster as a penalty) without adding an element.

Below is the code:

/*
 * File:   1829SimonSays.c
 * Author: Nicholas Gallagher
 *
 * Created on February 10, 2016, 10:34 AM
 */



#include <stdio.h>
#include <stdlib.h>


#include <htc.h>            //PIC hardware mapping

//Set configuration bits
__CONFIG(FOSC_INTOSC & WDTE_OFF & PWRTE_OFF & MCLRE_OFF & CP_OFF & CPD_OFF & BOREN_ON & CLKOUTEN_OFF & IESO_OFF & FCMEN_OFF);
__CONFIG(WRT_OFF & PLLEN_OFF & STVREN_OFF & LVP_OFF);

#define _XTAL_FREQ 500000

#define LED0 LATCbits.LATC2
#define LED1 LATCbits.LATC1
#define LED2 LATCbits.LATC0

#define SWITCH0 PORTCbits.RC4
#define SWITCH1 PORTCbits.RC5
#define SWITCH2 PORTAbits.RA5

#define MAXLEN 30

unsigned char sequence[MAXLEN];
unsigned char order;
unsigned char length;
unsigned short playDelay;

unsigned char save = 0;

//Declarations
unsigned char ChooseRandomElement();
void AddElement();
void PlaySequence();

void Setup()
{
    //Set timer mode
    OPTION_REGbits.TMR0CS = 0;  //Set TMR0 to increment once per instruction cycle
    OPTION_REGbits.T0CS = 0;    //Use internal clock as TMR0 source
    OPTION_REGbits.PSA = 1;     //Assign prescaler to WDT so TMR0 updates 1:1 with internal clock
   
    TRISCbits.TRISC2 = 0;
    TRISCbits.TRISC1 = 0;
    TRISCbits.TRISC0 = 0;
   
    LED0 = 0;
    LED1 = 0;
    LED2 = 0;
   
    TRISCbits.TRISC4 = 1;
    TRISCbits.TRISC5 = 1;
    TRISAbits.TRISA5 = 1;
   
    order = 0;
    length = 0;
   
    playDelay = 500;
   
    for(int i = 0; i < MAXLEN; i++) sequence[i] = (unsigned char)0;
    for(int i = 0; i < 5; i++)
    {
        AddElement();
    }
}

///
//Returns "random" number between 0 and 3 (non inclusive))
unsigned char ChooseRandomElement()
{
    char lfsr = TMR0bits.TMR0;
    char b = 0;
    for(int i = 0; i < 10; i++)
    {
        b = ((lfsr >> 0) ^ (lfsr >> 2) ^ (lfsr >> 3) ^ (lfsr >> 5)) & 1;
        lfsr = ((lfsr >> 1) | (b << 7));
    }
   
    unsigned char retVal = (save ^ lfsr)%3;
    save = (lfsr * lfsr - lfsr);
    return retVal;
}

void AddElement()
{
    if(length >= MAXLEN) length = 0;
   
    unsigned char numChosen = ChooseRandomElement();
    sequence[length++] = numChosen;
   
}

void PlaySequence()
{
    for(int i = 0; i < length; i++)
    {
        switch(sequence[i])
        {
            case 0:     //Toggle LED 0
                LED0 = 1;
                break;
            case 1:
                LED1 = 1;
                break;
            case 2:
                LED2 = 1;
                break;
            default:
                LED0 = 1;
                LED1 = 1;
                LED2 = 1;
                break;
               
               
        }
       
        for(int j = 0; j < playDelay; j++)
        {
            __delay_ms(1);
        }
        switch(sequence[i])
        {
            case 0:     //Toggle LED 0
                LED0 = 0;
                break;
            case 1:
                LED1 = 0;
                break;
            case 2:
                LED2 = 0;
                break;
            default:
                LED0 = 0;
                LED1 = 0;
                LED2 = 0;
                break;
               
        }
       
        for(int j = 0; j < playDelay; j++)
        {
            __delay_ms(1);
        }
    }
   
}

unsigned char GetButtonPress()
{
    while(1)
    {
        if(!SWITCH0)
        {
            LED0 = 1;
            return 0;
        }
        else if(!SWITCH1)
        {
            LED1 = 1;
            return 1;
        }
        else if(!SWITCH2)
        {
            LED2 = 1;
            return 2;
        }
    }
}

void GetButtonRelease()
{
    while(1)
    {
        if(SWITCH0 && SWITCH1 && SWITCH2) break;
    }
    LED0 = LED1 = LED2 = 0;
}

///
//Returns 0 if Listening was successful
unsigned char Listen()
{
    for(int i = 0; i < length; i++)
    {
        unsigned char selection = GetButtonPress();
        if(selection != sequence[i]) return 1;
        GetButtonRelease();
    }
    return 0;
}

void Win()
{
    for(int i = 0; i < 10; i++)
    {
        LED0 = 1;
        __delay_ms(100);
        LED0 = 0;
        LED1 = 1;
        __delay_ms(100);
        LED1 = 0;
        LED2 = 1;
        __delay_ms(100);
        LED2 = 0;
    }
    AddElement();
}

void Lose()
{
    for(int i = 0; i < 3; i++)
    {
        LED0 = LED1 = LED2 = 1;
        __delay_ms(1000);
        LED0 = LED1 = LED2 = 0;
        __delay_ms(1000);
    }
   
    playDelay -= 20;

}

/*
 *
 */
int main(int argc, char** argv)
{
    Setup();
   
    while(1)
    {
        PlaySequence();
   
        unsigned char result = Listen();
   
       
       
        if(result)
        {
            Lose();
        }
        else
        {
            __delay_ms(500);
            Win();
        }
       
        __delay_ms(1000);
    }

    return (EXIT_SUCCESS);
}	
