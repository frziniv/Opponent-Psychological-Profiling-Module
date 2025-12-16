# Opponent-Psychological-Profiling-Module
// PsychProfiler.ja// PsychProfiler.java (Updated Implementation)

import java.util.HashMap;
import java.util.Map;

public class PsychProfiler {

    private static final double AGGRESSIVE_THRESHOLD = 0.75;
    private static final double PASSIVE_THRESHOLD = 0.25;
    
    // NEW: Factor to boost aggressiveness if the opponent is on a losing streak
    private static final double LOSS_STREAK_BOOST = 0.15; 

    /**
     * Calculates the Dynamic Aggressiveness Score (DAS) of an opponent.
     * @param opponentRank Current rank of the opponent.
     * @param agentRank Our current rank.
     * @param timeRemaining Percentage of game time remaining (0.0 to 1.0).
     * @param opponentLossStreak Number of consecutive losses/failures the opponent has recently experienced.
     * @returns The calculated DAS score.
     */
    public double calculateDynamicAggressivenessScore(int opponentRank, int agentRank, double timeRemaining, int opponentLossStreak) {
        
        double rankDifferenceFactor = (double)(opponentRank - agentRank);
        if (rankDifferenceFactor < 0) {
            rankDifferenceFactor = rankDifferenceFactor * 0.5; 
        }
        
        double timeFactor = 1.0 - timeRemaining; 
        
        // Calculate the base score
        double rawScore = (rankDifferenceFactor * 0.5) + (timeFactor * 0.5);
        
        // NEW EDIT: Apply Loss Streak Boost
        if (opponentLossStreak >= 2) {
            // Apply a direct boost to aggressiveness if they are desperate
            rawScore += LOSS_STREAK_BOOST; 
        }
        
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
    
    // Example Usage
    public static void main(String[] args) {
        PsychProfiler profiler = new PsychProfiler();
        
        // Scenario 1: Opponent is slightly behind, mid-game, no streak
        double score1 = profiler.calculateDynamicAggressivenessScore(3, 1, 0.5, 0); 
        System.out.printf("Scenario 1 DAS (3rd Rank, No Streak): %.2f -> %s\n", 
            score1, profiler.getPredictedProfile(score1)); // Should be BALANCED
            
        // Scenario 2: Opponent far behind, late game, high loss streak
        double score2 = profiler.calculateDynamicAggressivenessScore(5, 1, 0.1, 3); 
        System.out.printf("Scenario 2 DAS (5th Rank, 3 Loss Streak): %.2f -> %s\n", 
            score2, profiler.getPredictedProfile(score2)); // Should be RECKLESS_AGGRESSIVE
    }
}
