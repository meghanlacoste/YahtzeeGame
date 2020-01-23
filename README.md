# YahtzeeGame

using static System.Console;

namespace Bme121
{
    static class Program
    {
        public static void Main( )
        {
            YahtzeePlayer[ ] players;

            WriteLine( );
            WriteLine( "The game of Yahtzee is (TM) and (C) Hasbro Inc." );
            WriteLine( "This implementation is for research and educational purposes only." );

            WriteLine( );
            Write( "Enter the number of players:  " );
            int numPlayers = int.Parse( ReadLine( ) );

            players = new YahtzeePlayer[ numPlayers ];
            for( int n = 1; n <= numPlayers; n ++ )
            {
                Write( "Enter the name of player {0}:  ", n );
                players[ n - 1 ] = new YahtzeePlayer( ReadLine( ) );
            }

            for( int turn = 1; turn <= 13; turn ++ )
            {
                foreach( YahtzeePlayer player in players )
                {
                    player.Dice.Unroll( "all" );

                    WriteLine( );
                    WriteLine( );
                    WriteLine( player.ScoreCard );
                    WriteLine( );
                    WriteLine( );
                    WriteLine( "Turn {0} for {1}", turn, player );
                    WriteLine( );
                    WriteLine( );
                    WriteLine( "Ready to roll" );
                    Write( "Press <Enter> to roll the dice" );
                    ReadLine( );

                    player.Dice.Roll( );
                    WriteLine( );
                    WriteLine( );
                    WriteLine( player.ScoreCard );
                    WriteLine( );
                    WriteLine( player.Dice );
                    WriteLine( );
                    WriteLine( "First roll, ready to re-roll" );
                    Write( "Enter the face values to re-roll:  " );
                    player.Dice.Unroll( ReadLine( ) );

                    player.Dice.Roll( );
                    WriteLine( );
                    WriteLine( );
                    WriteLine( player.ScoreCard );
                    WriteLine( );
                    WriteLine( player.Dice );
                    WriteLine( );
                    WriteLine( "Second roll, ready to re-roll" );
                    Write( "Enter the face values to re-roll:  " );
                    player.Dice.Unroll( ReadLine( ) );

                    player.Dice.Roll( );
                    WriteLine( );
                    WriteLine( );
                    WriteLine( player.ScoreCard );
                    WriteLine( );
                    WriteLine( player.Dice );
                    WriteLine( );
                    WriteLine( "Last roll, ready to score" );
                    Write( "Enter the line to be scored:  " );
                    string lineCode = ReadLine( );

                    bool scoreAccepted = player.ScoreCard.TryScore( player.Dice, lineCode );

                    while( ! scoreAccepted )
                    {
                        WriteLine( );
                        WriteLine( );
                        WriteLine( player.ScoreCard );
                        WriteLine( );
                        WriteLine( player.Dice );
                        WriteLine( );
                        Write( "LineCode \"{0}\" is used/", lineCode );
                        WriteLine( "invalid, ready to try again" );
                        Write( "Enter the line to be scored:  " );
                        lineCode = ReadLine( );

                        scoreAccepted = player.ScoreCard.TryScore( player.Dice, lineCode );
                    }

                    WriteLine( );
                    WriteLine( );
                    WriteLine( player.ScoreCard );
                    WriteLine( );
                    WriteLine( player.Dice );
                    WriteLine( );

                    if( turn == 13 )
                    {
                        Write( "Scored on line [{0}], ", lineCode );
                        WriteLine( " ready to add" );
                        Write( "Press <Enter> to add up your scores:  " );
                        ReadLine( );

                        player.ScoreCard.Calculate( "SubtotalUpper" );
                        player.ScoreCard.Calculate( "BonusUpper" );
                        player.ScoreCard.Calculate( "TotalUpper" );
                        player.ScoreCard.Calculate( "YahtzeeBonus" );
                        player.ScoreCard.Calculate( "TotalLower" );
                        player.ScoreCard.Calculate( "GrandTotal" );

                        WriteLine( );
                        WriteLine( );
                        WriteLine( player.ScoreCard );
                        WriteLine( );
                        WriteLine( );
                        WriteLine( "End of game for {0}", player );
                        WriteLine( );
                        WriteLine( );
                        WriteLine( "Ready to record grand total" );
                        Write( "Press <Enter> to continue:  " );
                        ReadLine( );
                    }
                    else
                    {
                        Write( "Scored on line [{0}], ", lineCode );
                        WriteLine( " ready for next turn/player" );
                        Write( "Press <Enter> to continue:  " );
                        ReadLine( );
                    }
                }
            }

            WriteLine( );
            WriteLine( "Final scores" );
            WriteLine( );
            foreach( YahtzeePlayer player in players )
            {
                WriteLine( "{0}  {1}", player, player.ScoreCard.GrandTotal );
            }
        }
    }
}
