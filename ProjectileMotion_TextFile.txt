// Author   : Christian Dunn
// Date     : Feb 1st 2015
// Purpose  : To complete simple projectile motion problems using given motion equations
// Compiler : Visual Studio 2013 and 2015

#define _USE_MATH_DEFINES																			// define _USE_MATH_DEFINES to access PI value stored in math.h header file

#include <iostream>																					// Include file to access operators defined in class
#include <cmath>																					// Include file to access operators defined in class
#include <fstream>																					// Include file to access operators defined in class
#include <math.h>																					// Include file to access operators defined in class
#include <iomanip>																					// Include file to access operators defined in class
#include "windows.h"																				// Include file to access operators defined in class
using namespace std;

const double GRAVITY        = 9.8       ;															// Define constant value for gravity to be used amongst functions
const double SPEED_OF_LIGHT = 300000000 ;															// Define constant value for the speed of light to be used amongst functions
const int    TIME_STEPS     = 30        ;															// Define constant value for the amount of steps given to output file

void   title        ()                          ;													// Function prototype for function defined outside main
double conversion   (double)                    ;													// Function prototype for function defined outside main
double horizVel     (double, double)            ;													// Function prototype for function defined outside main
double vertVel      (double, double)            ;													// Function prototype for function defined outside main
double distanceProj (double, double)            ;													// Function prototype for function defined outside main
double flightTime   (double, double)            ;													// Function prototype for function defined outside main
void   heightAtTime (double, double, ostream &) ;													// Function prototype for function defined outside main
void   writeToFile  (double, double)            ;													// Function prototype for function defined outside main

// Function   : MAIN
// Purpose    : To compile and test defined functions used in accomplishing objective
// Parameters : double theta				double initialVel
//				bool   valid
int main()
	{
		double   theta, initialVel ;																// Define variables of type double to hold various user input
		bool     valid = false     ;																// Define variable of type boolean to be used in checking validity of input

		system("COLOR 02");
		title();																					// Display title of program

		cout << "Enter angle above horizontal axis (in degrees) : ";								// Prompt user to input angle theta

		while (!valid)																				// While loop to test validity of user input
			{
				cin  >> theta ;																		// Store user input in variable theta
				cout << endl  ;			

				valid = true  ;																		// Assume user entered a valid input

				if (cin.fail())																		// Test if the input was the correct data type
					{
						system("cls") ;																
						title()       ;

						cin.clear()                                                ;				// If data was not correct data type clear memory stored in variable
						cin.ignore()                                               ;				// Ignore previous cin and empty spot in memory		
						cout << "Invalid variable type! Enter a numerical value: " ;				// Prompt user to enter valid value
						valid = false                                              ;				// Set validity equal to false to run through check again
					} // if

				else if ((theta <= 0) || (theta >= 90))												// If statement to test the bounds of theta			
					{
						system("cls") ;
						title()       ;

						cout << "Invalid angel theta! Enter appropriate theta value: " ;			// If not valid value, prompt user to enter new value
						valid = false                                                  ;			// Set validity equal to false to run through check again
					} // else if
			} // while

		valid = false;																				// Set validity equal to false for velocity check

		cout << "Enter the initial velocity of the object (m/s) : ";								// Prompt user to enter velocity

		while (!valid)																				// While statement to test validity of velocity input
			{
				cin  >> initialVel ;																// Read in user input and store in velocity variable
				cout << endl       ;

				valid = true       ;																// Assume user's input is of correct type

				if (cin.fail())																		// Test to confirm input is correct data type
					{
						system("cls") ;
						title()       ;

						cin.clear()                                                ;				// If data type is not correct clear data stored in member variable
						cin.ignore()                                               ;				// Ignore the previous input data and command
						cout << "Invalid variable type! Enter a numerical value: " ;				// Prompt user to enter new valid velocity value
						valid = false                                              ;				// Set validity equal to false to run through check again
					} // if

				else if (initialVel <= 0)															// Test to make sure value is not negative
					{
						system("cls") ;
						title()       ;

						cout << "Invalid Velocity! Enter Positive velocity value: " ;				// If value is negative prompt user to enter new value
						valid = false                                               ;				// Set validity equal to false to run through check again
					} // else if

				else if (initialVel >= SPEED_OF_LIGHT)												// Test to make sure value is not faster than the speed of light
					{
						system("cls") ;
						title()       ;

						cout << "Nice try! Enter valid velocity : " ;								// Prompt user to enter new valid value for velocity
						valid = false                               ;								// Set validity equal to false to run through check again
					} // else if
			} // while

		system("cls") ;
		title()       ;

		cout << "Overall Flight Time : " << setprecision(4) << fixed 
										 << flightTime(initialVel, theta) << " seconds\n\n"  ;		// Display overall flight time of projectile

		cout << "Horizontal Distance : " << setprecision(4) << fixed
										 << distanceProj(initialVel, theta) << " meters\n\n" ;		// Display horizontal distance traveled by projectile

		writeToFile(vertVel(initialVel, theta), theta)                                       ;		// Write data for 30 times to file

		system("pause") ;
		return 0        ;
	} // main

// Function   : CONVERSION
// Purpose    : To convert user angle in degrees to radians
// Parameters : double theta
double conversion(double theta)																		// Define function that converts degrees to radians
	{ return theta * (M_PI / 180); } // conversion													// Return degrees converted to radians

// Function   : HORIZVEL
// Purpose    : To calculate horizontal velocity component used in finding horizontal distance
// Parameters : double vel					double theta
double horizVel(double vel, double theta)															// Define function that calculates horizontal velocity of initial velocity vector
	{ return vel * cos(conversion(theta)); } // horizVel											// Return horizontal velocity calculation

// Function   : VERTVEL
// Purpose    : To calculate vertical velocity component used in finding time and vertical height
// Parameters : double vel					double theta
double vertVel(double vel, double theta)															// Define function that calculates vertical velocity of initial velocity vector
	{ return vel * sin(conversion(theta)); } // vertVel												// Return vertical velocity calculation

// Function   : DISTANCEPROJ
// Purpose    : To calculate the horizontal distance the projectile travels in given time
// Parameters : double vel					double theta
double distanceProj(double vel, double theta)														// Define function that calculates horizontal distance projectile travels
	{ return horizVel(vel, theta) * flightTime(vel, theta); } // distanceProj						// Return horizontal distance calculation

// Function   : FLIGHTTIME
// Purpose    : To calculate the amount of time the projectile travels through the air
// Parameters : double vel					double theta
double flightTime(double vel, double theta)															// Define function that calculates the flight time of the projectile
	{ return 2 * (vertVel(vel, theta) / GRAVITY); } // flightTime									// Return time in flight calculation

// Function   : HEIGHTATTIME
// Purpose    : To calcualte the height of the projectile at a given time T
// Parameters : double   vel					double theta
//				ostream& stream				    double timeFrac
//				double   time					double square
//				double   height[TIME_STEPS]     int    i
void heightAtTime(double vel, double theta, ostream &stream)										// Define function to determine height at given time increments
	{
		double timeFrac           = flightTime(vel, theta) / TIME_STEPS ;							// Define a variable to hold a fraction of time
		double time               = 0.0000                              ;							// Define an initial value for time
		double square             = 2.0                                 ;							// Define variable used for power when squaring time
		double height[TIME_STEPS]                                       ;							// Define array to hold height values at given times

		height[0] = 0.0000                                              ;							// Set initial height to zero at time zero

		stream << setw(15) << height[0] << setw(15) << time << endl     ;							// Write initial values to file

		for (int i = 1; i < TIME_STEPS; i++)														// For loop to calculate height values at each fraction of time
			{
				time = time + timeFrac                                              ;				// Calculate time variable at given point

				height[i] = (vel*(time)) - ((0.5)*(GRAVITY)*(pow(time, square)))    ;				// Calculate height at each given time interval

				stream << setw(15) << setprecision(4) << fixed << time 
					   << setw(15) << setprecision(4) << fixed << height[i] << endl ;				// Write data to file
			} // for

	} // heightAtTime

// Function   : WRITETOFILE
// Purpose    : To create a text file and write flight data of projectile
// Parameters : double   vel					double theta
//				ofstream outputFile
void writeToFile(double vel, double theta)															// Define function used to write data to file 
	{
		ofstream outputFile                     ;													// Define variable of type ofstream used to create output file
	
		outputFile.open("ProjectileMotion.txt") ;													// Open created file

		outputFile << setw(15) << "Time (s)" << setw(15) << "Height (m)" << endl ;					// Write header for data
		outputFile << "______________________________________________" << endl   ;					// Write header for data

		heightAtTime(vel, theta, outputFile) ;														// Call heightAtTime function to write data to file

		outputFile.close()                   ;														// Close file used to write data
	} // writeToFile 

// Function   : TITLE
// Purpose    : To output title prompt to notify user of the objective of the program
// Parameters :
void title()
	{
		cout << "_____________________________________________\n"     ;								// Display title prompt
		cout << " Welcome to the Projectile Motion Simulator!\n"      ;								// Display title prompt
		cout << "_____________________________________________\n\n\n" ;								// Display title prompt
	} // title