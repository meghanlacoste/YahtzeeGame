# YahtzeeGame
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
