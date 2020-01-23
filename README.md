# YahtzeeGame
using System;
using System.Text;

namespace Bme121
{
    class YahtzeeScoreCard
    {
        readonly string playerName;
        readonly YahtzeeScoreBox ones;
        readonly YahtzeeScoreBox twos;
        readonly YahtzeeScoreBox threes;
        readonly YahtzeeScoreBox fours;
        readonly YahtzeeScoreBox fives;
        readonly YahtzeeScoreBox sixes;
        readonly YahtzeeScoreBox subtotalUpper;
        readonly YahtzeeScoreBox bonusUpper;
        readonly YahtzeeScoreBox totalUpper;
        readonly YahtzeeScoreBox threeOfAKind;
        readonly YahtzeeScoreBox fourOfAKind;
        readonly YahtzeeScoreBox fullHouse;
        readonly YahtzeeScoreBox smallStraight;
        readonly YahtzeeScoreBox largeStraight;
        readonly YahtzeeScoreBox yahtzee;
        readonly YahtzeeScoreBox chance;
        readonly YahtzeeScoreBox yahtzeeBonus;
        readonly YahtzeeScoreBox totalLower;
        readonly YahtzeeScoreBox grandTotal;
        int yahtzeeCount;

        public YahtzeeScoreCard( string playerName )
        {
            this.playerName = playerName;

            string shaded = new string( '\u2591', 4 );
            string blanks = new string( ' ', 4 );

            ones          = new YahtzeeScoreBox( blanks, "XXXX" );
            twos          = new YahtzeeScoreBox( blanks, "XXXX" );
            threes        = new YahtzeeScoreBox( blanks, "XXXX" );
            fours         = new YahtzeeScoreBox( blanks, "XXXX" );
            fives         = new YahtzeeScoreBox( blanks, "XXXX" );
            sixes         = new YahtzeeScoreBox( blanks, "XXXX" );
            subtotalUpper = new YahtzeeScoreBox( shaded, "   0" );
            bonusUpper    = new YahtzeeScoreBox( shaded, "XXXX" );
            totalUpper    = new YahtzeeScoreBox( shaded, "   0" );
            threeOfAKind  = new YahtzeeScoreBox( blanks, "XXXX" );
            fourOfAKind   = new YahtzeeScoreBox( blanks, "XXXX" );
            fullHouse     = new YahtzeeScoreBox( blanks, "XXXX" );
            smallStraight = new YahtzeeScoreBox( blanks, "XXXX" );
            largeStraight = new YahtzeeScoreBox( blanks, "XXXX" );
            yahtzee       = new YahtzeeScoreBox( blanks, "XXXX" );
            chance        = new YahtzeeScoreBox( blanks, "XXXX" );
            yahtzeeBonus  = new YahtzeeScoreBox( shaded, "XXXX" );
            totalLower    = new YahtzeeScoreBox( shaded, "   0" );
            grandTotal    = new YahtzeeScoreBox( shaded, "   0" );

            yahtzeeCount  = 0;
        }

        public string PlayerName
        {
            get
            {
                return playerName;
            }
        }

        public int GrandTotal
        {
            get
            {
                return grandTotal.Score;
            }
        }

        public bool TryScore ( YahtzeeDice dice, string lineCode )
        {
            bool ok = false;
            switch( lineCode )
            {
                case "1s":
                    ok = ones.TryScore(
                        dice.Sum( 1 ) );
                    break;

                case "2s":
                    ok = twos.TryScore(
                        dice.Sum( 2 ) );
                    break;

                case "3s":
                    ok = threes.TryScore(
                        dice.Sum( 3 ) );
                    break;

                case "4s":
                    ok = fours.TryScore(
                        dice.Sum( 4 ) );
                    break;

                case "5s":
                    ok = fives.TryScore(
                        dice.Sum( 5 ) );
                    break;

                case "6s":
                    ok = sixes.TryScore(
                        dice.Sum( 6 ) );
                    break;

                case "3k":
                    ok = threeOfAKind.TryScore(
                        dice.IsSetOf( 3 ) ? dice.Sum( ) : 0 );
                    break;

                case "4k":
                    ok = fourOfAKind.TryScore(
                        dice.IsSetOf( 4 ) ? dice.Sum( ) : 0 );
                    break;

                case "fh":
                    ok = fullHouse.TryScore(
                        IsWild( dice ) || dice.IsFullHouse( ) ? 25 : 0 );
                    break;

                case "sm":
                    ok = smallStraight.TryScore(
                        IsWild( dice ) || dice.IsRunOf( 4 ) ? 30 : 0 );
                    break;

                case "lg":
                    ok = largeStraight.TryScore(
                        IsWild( dice ) || dice.IsRunOf( 5 ) ? 40 : 0 );
                    break;

                case "ya":
                    ok = yahtzee.TryScore(
                        dice.IsSetOf( 5 ) ? 50 : 0 );
                    break;

                case "ch":
                    ok = chance.TryScore(
                        dice.Sum( ) );
                    break;

                default  :                                                                                       break;
            }

            if( ok )
            {
                if( dice.IsSetOf( 5 ) )
                {
                    if( yahtzeeCount == 0 && lineCode != "ya" )
                        yahtzeeBonus.Score = 0;
                    if( IsYahtzeeBonus( dice ) )
                        yahtzeeBonus.Score += 100;
                    yahtzeeCount ++;
                }
                else
                {
                    if( yahtzeeCount == 0 && lineCode == "ya" )
                        yahtzeeBonus.Score = 0;
                }
            }

            return ok;
        }

        public void Calculate( string lineName )
        {
            switch( lineName )
            {
                case "SubtotalUpper":
                    subtotalUpper.Score =
                        ones.Score
                      + twos.Score
                      + threes.Score
                      + fours.Score
                      + fives.Score
                      + sixes.Score;
                    break;

                case "BonusUpper":
                    bonusUpper.Score = subtotalUpper.Score >= 63 ? 35 : 0;
                    break;

                case "TotalUpper":
                    totalUpper.Score = subtotalUpper.Score + bonusUpper.Score;
                    break;

                case "YahtzeeBonus":
                    if( ! yahtzeeBonus.Used ) yahtzeeBonus.Score = 0;
                    break;

                case "TotalLower":
                    totalLower.Score =
                        threeOfAKind.Score
                      + fourOfAKind.Score
                      + fullHouse.Score
                      + smallStraight.Score
                      + largeStraight.Score
                      + yahtzee.Score
                      + chance.Score
                      + yahtzeeBonus.Score;
                    break;

                case "GrandTotal":
                    grandTotal.Score = totalUpper.Score + totalLower.Score;
                    break;

                default:
                    string message = "value = \"{0}\" not recognized";
                    throw new ArgumentOutOfRangeException( "lineName",
                        string.Format( message, lineName ) );
            }
        }

        bool IsWild( YahtzeeDice dice )
        {
            if( ! dice.IsSetOf( 5 ) || yahtzeeCount == 0 ) return false;
            switch( dice.Sum( ) / 5 )
            {
                case 1:  return ones.Used;
                case 2:  return twos.Used;
                case 3:  return threes.Used;
                case 4:  return fours.Used;
                case 5:  return fives.Used;
                case 6:  return sixes.Used;
                default: return false;
            }
        }

        bool IsYahtzeeBonus( YahtzeeDice dice )
        {
            if( ! dice.IsSetOf( 5 ) || yahtzeeCount == 0 ) return false;
            if( ! yahtzeeBonus.Used || yahtzeeBonus.Score > 0 ) return true;
            return false;
        }

        public override string ToString( )
        {
            string name;
            if( playerName.Length > 13 ) name = playerName.Remove( 13 );
            else name = playerName;

            StringBuilder sb = new StringBuilder( );

            const char hl = '\u2500'; // horizontal line
            const char vl = '\u2502'; // vertical line
            const char tl = '\u250c'; // top left corner
            const char tr = '\u2510'; // top right corner
            const char ml = '\u251c'; // middle left joint
            const char mr = '\u2524'; // middle right joint
            const char bl = '\u2514'; // bottom left corner
            const char br = '\u2518'; // bottom right corner

            // Score card content lines, each 53 characters, with placeholders
            string cnt02 = "SCORE CARD              Player  [ {0,13} ]";
            string cnt04 = "[1s]  Ones              Add only ones    [ {0,4} ]";
            string cnt05 = "[2s]  Twos              Add only twos    [ {0,4} ]";
            string cnt06 = "[3s]  Threes            Add only threes  [ {0,4} ]";
            string cnt07 = "[4s]  Fours             Add only fours   [ {0,4} ]";
            string cnt08 = "[5s]  Fives             Add only fives   [ {0,4} ]";
            string cnt09 = "[6s]  Sixes             Add only sixes   [ {0,4} ]";
            string cnt10 = "      Subtotal                           [ {0,4} ]";
            string cnt11 = "      Bonus for 63+     Score 35         [ {0,4} ]";
            string cnt12 = "      Total upper                        [ {0,4} ]";
            string cnt13 = "[3k]  3 of a kind       Add all dice     [ {0,4} ]";
            string cnt14 = "[4k]  4 of a kind       Add all dice     [ {0,4} ]";
            string cnt15 = "[fh]  Full house        Score 25         [ {0,4} ]";
            string cnt16 = "[sm]  Small seq of 4    Score 30         [ {0,4} ]";
            string cnt17 = "[lg]  Large seq of 5    Score 40         [ {0,4} ]";
            string cnt18 = "[ya]  Yahtzee           Score 50         [ {0,4} ]";
            string cnt19 = "[ch]  Chance            Add all dice     [ {0,4} ]";
            string cnt20 = "      Yahtzee bonus     Score 100 each   [ {0,4} ]";
            string cnt21 = "      Total lower                        [ {0,4} ]";
            string cnt22 = "      Grand total                        [ {0,4} ]";
            string hline = new string( hl, 53 );  // Horizontal line for borders

            // Framed content lines
            string fmt01 = string.Format( "{0}{1}{2}"    , tl, hline, tr );
            string fmt02 = string.Format( "{0}  {1}  {2}", vl, cnt02, vl );
            string fmt03 = string.Format( "{0}{1}{2}"    , ml, hline, mr );
            string fmt04 = string.Format( "{0}  {1}  {2}", vl, cnt04, vl );
            string fmt05 = string.Format( "{0}  {1}  {2}", vl, cnt05, vl );
            string fmt06 = string.Format( "{0}  {1}  {2}", vl, cnt06, vl );
            string fmt07 = string.Format( "{0}  {1}  {2}", vl, cnt07, vl );
            string fmt08 = string.Format( "{0}  {1}  {2}", vl, cnt08, vl );
            string fmt09 = string.Format( "{0}  {1}  {2}", vl, cnt09, vl );
            string fmt10 = string.Format( "{0}  {1}  {2}", vl, cnt10, vl );
            string fmt11 = string.Format( "{0}  {1}  {2}", vl, cnt11, vl );
            string fmt12 = string.Format( "{0}  {1}  {2}", vl, cnt12, vl );
            string fmt13 = string.Format( "{0}  {1}  {2}", vl, cnt13, vl );
            string fmt14 = string.Format( "{0}  {1}  {2}", vl, cnt14, vl );
            string fmt15 = string.Format( "{0}  {1}  {2}", vl, cnt15, vl );
            string fmt16 = string.Format( "{0}  {1}  {2}", vl, cnt16, vl );
            string fmt17 = string.Format( "{0}  {1}  {2}", vl, cnt17, vl );
            string fmt18 = string.Format( "{0}  {1}  {2}", vl, cnt18, vl );
            string fmt19 = string.Format( "{0}  {1}  {2}", vl, cnt19, vl );
            string fmt20 = string.Format( "{0}  {1}  {2}", vl, cnt20, vl );
            string fmt21 = string.Format( "{0}  {1}  {2}", vl, cnt21, vl );
            string fmt22 = string.Format( "{0}  {1}  {2}", vl, cnt22, vl );
            string fmt23 = string.Format( "{0}{1}{2}"    , bl, hline, br );

            // Framed content lines with values inserted
            sb.AppendLine               ( fmt01                );
            sb.AppendLine( string.Format( fmt02, name          ) );
            sb.AppendLine               ( fmt03                );
            sb.AppendLine( string.Format( fmt04, ones          ) );
            sb.AppendLine( string.Format( fmt05, twos          ) );
            sb.AppendLine( string.Format( fmt06, threes        ) );
            sb.AppendLine( string.Format( fmt07, fours         ) );
            sb.AppendLine( string.Format( fmt08, fives         ) );
            sb.AppendLine( string.Format( fmt09, sixes         ) );
            sb.AppendLine( string.Format( fmt10, subtotalUpper ) );
            sb.AppendLine( string.Format( fmt11, bonusUpper    ) );
            sb.AppendLine( string.Format( fmt12, totalUpper    ) );
            sb.AppendLine( string.Format( fmt13, threeOfAKind  ) );
            sb.AppendLine( string.Format( fmt14, fourOfAKind   ) );
            sb.AppendLine( string.Format( fmt15, fullHouse     ) );
            sb.AppendLine( string.Format( fmt16, smallStraight ) );
            sb.AppendLine( string.Format( fmt17, largeStraight ) );
            sb.AppendLine( string.Format( fmt18, yahtzee       ) );
            sb.AppendLine( string.Format( fmt19, chance        ) );
            sb.AppendLine( string.Format( fmt20, yahtzeeBonus  ) );
            sb.AppendLine( string.Format( fmt21, totalLower    ) );
            sb.AppendLine( string.Format( fmt22, grandTotal    ) );
            sb.Append                   ( fmt23                );

            return sb.ToString( );
        }
    }
}

using System;

namespace Bme121
{
    class YahtzeeScoreBox
    {
        bool used;
        int score;
        readonly string unusedFormat;
        readonly string zeroFormat;

        public YahtzeeScoreBox( string unusedFormat, string zeroFormat )
        {
            this.used = false;
            this.score = 0;
            this.unusedFormat = unusedFormat;
            this.zeroFormat = zeroFormat;
        }

        public bool Used
        {
            get
            {
                return used;
            }
        }

        public int Score
        {
            get
            {
                return score;
            }
            set
            {
                score = value;
                used = true;
            }
        }

        public bool TryScore( int value )
        {
            if( used ) return false;
            score = value;
            used = true;
            return true;
        }

        public override string ToString( )
        {
            if( ! used ) return unusedFormat;
            if( score == 0 ) return zeroFormat;
            return score.ToString( );
        }
    }
}

        public bool TryScore( int value )
        {
            if( used ) return false;
            score = value;
            used = true;
            return true;
        }

        public override string ToString( )
        {
            if( ! used ) return unusedFormat;
            if( score == 0 ) return zeroFormat;
            return score.ToString( );
        }
    }
}
