# YahtzeeGame

using System;
namespace Bme121
{
    class YahtzeePlayer
    {
        readonly YahtzeeDice dice;
        readonly YahtzeeScoreCard scoreCard;

        public YahtzeePlayer( string playerName )
        {
            dice = new YahtzeeDice( );
            scoreCard = new YahtzeeScoreCard( playerName );
        }

        public YahtzeeDice Dice
        {
            get { return dice; }
        }

        public YahtzeeScoreCard ScoreCard
        {
            get { return scoreCard; }
        }

        public override string ToString( )
        {
            return scoreCard.PlayerName;
        }
    }
}
