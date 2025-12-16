# Opponent-Psychological-Profiling-Module
// PsychProfiler.java

import java.util.HashMap;
import java.util.Map;

public class PsychProfiler {

    // Thresholds for determining behavioral profile
    private static final double AGGRESSIVE_THRESHOLD = 0.75;
    private static final double PASSIVE_THRESHOLD = 0.25;

    /**
     * Calculates the Dynamic Aggressiveness Score (DAS) of an opponent.
     * DAS is an estimate of how likely the opponent is to take high risks (0.0 to 1.0).
     * @param opponentRank Current rank of the opponent (1 is best).
     * @param agentRank Our current rank.
     * @param timeRemaining Percentage of game time remaining (0.0 to 1.0).
     * @returns The calculated DAS score.
     */
    public double calculateDynamicAggressivenessScore(int opponentRank, int agentRank, double timeRemaining) {
        
        // Basic calculation: Aggressiveness increases the further behind they are, 
        // especially towards the end of the game.
        double rankDifferenceFactor = (double)(opponentRank - agentRank);
        if (rankDifferenceFactor < 0) {
            // Opponent is ahead; they will likely be passive.
            rankDifferenceFactor = rankDifferenceFactor * 0.5; // Reduce impact
        }
        
        // Time factor: Closer to the end (low timeRemaining), the pressure increases.
        double timeFactor = 1.0 - timeRemaining; 
        
        // Simple formula: Normalize the result to be between 0 and 1.
        double rawScore = (rankDifferenceFactor * 0.5) + (timeFactor * 0.5);
        
        // Clamp the score between 0 and 1
        return Math.min(1.0, Math.max(0.0, rawScore));
    }

    public String getPredictedProfile(double dasScore) {
        if (dasScore >= AGGRESSIVE_THRESHOLD) {
            return "RECKLESS_AGGRESSIVE (Exploit vulnerabilities)";
        } else if (dasScore <= PASSIVE_THRESHOLD) {
            return "OVERLY_CAUTIOUS (Punish slow action)";
        } else {
            return "BALANCED (Maintain standard strategy)";
        }
    }
}
