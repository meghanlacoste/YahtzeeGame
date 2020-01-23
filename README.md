# YahtzeeGame
using static System.Console;
using System;

namespace Bme121
{
     class YahtzeeDice
    {
        // declares array as field
        int[] dice;
        static Random rGen = new Random ();
        
        //declares static int unrolled
        static int unrolled = 0;
           
        public YahtzeeDice()
        {   
            // intitializes dice array
             dice = new int [5];
        }
            
        // method assigns a randome value inbetween 1-6 for every dice
        // in the dice array
        public void Roll( ) 
        {
            for (int i = 0; i < 5; i++)
            {
               if (dice[i] == unrolled) dice[i] = rGen.Next(1,7);
            }
        }

        public void Unroll( string faces )
        { 
            // at the end of each turn all faces are set to be unrolled
            if (faces == "all")
            {
                for (int i = 0; i < 5; i++)
                {
                    dice[i] = unrolled;
                }
            } else 
            {
                // loops through all the characters in the string. 
                 for ( int i = 0; i < faces.Length; i++)
                {
                    // loops through all the dice in the dice array and if that die face
                    // value matches the character from the string array it is set to be unrolled.
                    
                    
                    for ( int i2 = 0; i2 < 5; i2 ++)
                    {
                        if ( dice[i2] == (int)(char.GetNumericValue(faces[i]))) dice [i2] = unrolled; 
                    }
                }
            }
        }

        // returns the sum of all the dice in the array
        public int Sum( ) 
        {
            int sum = 0;
            for (int i = 0; i < 5; i++)
            {
                sum += dice[i];
            }
            return sum; 
        }

        // returns the sum of all dice with a certain face value passed in
        public int Sum( int face )
        {   
            int sum = 0;
            // loops through all values in the dice array
            // and adds to sum if dice value matches parameter passed in
            for (int i = 0; i < 5; i ++)
            {
                if (dice[i] == face)  sum += face;
            }
            return sum; 
        }

        // returns boolean value of whether or not there is a run of the 
        // given 'length'
        public bool IsRunOf( int length ) 
        {   
            // creates a copy of the dice array
            int [] diceCopy = new int [5];
                    
            for (int i = 0; i < 5; i++)
            {
                diceCopy[i] = dice[i];
            }       
            // sorts the copy of the dice array so that original array is not altered
            Array.Sort(diceCopy);
                
            int index = 0;
            int counter = 0;

            // loops through the dice array to determine starting position of what
            // face value to check
            while (index + length <= 5) 
            {
                    // increments through the dice array from the starting position
                    // for 'length' number of times
                    for (int i = 0; i < length-1; i++)
                    {
                        // adds to counter if the value at the position is one less than
                        // the value in the next position 
                        if ((diceCopy[index+i+1] - diceCopy[index+i]) == 1) counter ++;
                        if (counter == length-2) return true;
                    }
                
                index ++;
                counter = 0;
            }
            // end while !run  
            return false;      
        }

        // returns boolean that indicates whether there is a set of a 
        // certain size 
        public bool IsSetOf( int size )
        { 
            int counter;
            int face = 1;
            //loops through possible face values 1-6
            while (face < 7)
            {
                counter = 0;
                // loops through the dice array
                for (int i = 0; i < 5; i++)
                {
                    // checks to see if there is a set of 'size' for the 
                    // certain face value
                    if (dice[i] == face)
                    {
                        counter ++;
                        if(counter == size) return true;
                    }
                } 
                face ++;
            }
            return false;
        }

        public bool IsFullHouse( ) 
        {
            bool setOfThree = false; 
            int counter;
            int threeOfKind = 0;
            int face = 1;

            // first checks if there is a set of three using same logic as 'IsSetOf' method
            while (!setOfThree && face < 7)
            {
                counter = 0;
                for (int i = 0; i < 5; i++)
                {
                    if (dice[i] == face)
                    {
                        counter ++;
                        if(counter == 3)
                        {
                            setOfThree = true;
                            threeOfKind = face;
                        }
                    }
                }
                face ++; 
            }
           
            face = 1;
            // if set of three checks if there is a set of two
            if (setOfThree)
            {
                while (face < 7) 
                {
                     counter = 0;
                    // loops through to test each dice in the array
                    for (int i = 0; i < 5; i++)
                    {
                        // makes sure that the set of two is not counting the set of 3
                        if (dice[i] == face && face!= threeOfKind)
                        {
                            counter ++;
                            //returns true if there is also a set of two
                            if (counter == 2) return true;
                        }    
                    }
                    face ++;
                } 
            }  
             return false;       
        }

        // returns each dice and their face value
        public override string ToString() 
        {
            string diceOutput = "";
            for (int die = 0; die < 5; die ++)
            {
                diceOutput += $" {dice[die]} \n";
            }
            return diceOutput;
        }
        
        
    }
}
