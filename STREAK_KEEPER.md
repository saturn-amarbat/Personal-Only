# GitHub Daily Streak Keeper Workflow

This document explains the "Daily Streak Keeper" workflow, located in `.github/workflows/streak-keeper.yml`, designed to help maintain your GitHub contribution streak with randomized commits.

## Purpose

The primary goal of this workflow is to automatically make two small commits to your repository each day. This ensures your GitHub contribution streak remains active, even if you forget to make manual commits. The commits are made at randomized times to minimize the impression of automated activity.

## Technology Used

*   **GitHub Actions:** The automation platform that runs this workflow.
*   **YAML:** The language used to define the workflow configuration (`.yml` file).
*   **Cron Syntax:** Used to schedule when the workflow runs.
*   **Bash Scripting:** Used within the workflow steps to perform file modifications, git operations, and introduce randomization.

## How it Works

The workflow operates based on a schedule and internal logic to achieve its goals:

1.  **Scheduled Triggers:** The workflow is scheduled to run multiple times throughout the day at specific UTC hours (currently 07:00, 11:00, 15:00, 19:00, 23:00). This provides several "opportunities" for the daily commits to occur.

2.  **Daily Commit Check:**
    *   Upon each scheduled trigger, the script first checks the `streak_log.txt` file to see if a contribution for the current day has already been recorded.
    *   If a contribution for today is found, the workflow exits immediately, preventing duplicate daily commits.

3.  **Randomization Logic:**
    *   If no contribution for the day has been made yet, the workflow enters a randomization phase.
    *   For any scheduled run *before* the last slot of the day (currently 23:00 UTC), there's a chance (currently 20%) that the workflow will randomly decide to *skip* this particular run. This introduces variability in the exact time of the commit.
    *   The last scheduled slot (23:00 UTC) is always forced to run if no commit has been made yet, ensuring that at least one attempt to make the commits happens daily.

4.  **Random Delay:**
    *   If the workflow decides to proceed (either by random chance or by being the forced last run), it introduces a random delay (between 0 and 15 minutes) before executing the commit commands. This further randomizes the exact minute of the commit, making the activity appear more natural.

5.  **Two-Commit Mechanism:**
    *   After any checks and delays, the workflow proceeds to make **two separate commits**.
    *   It appends a unique "Keep alive 1" entry to `streak_log.txt`, commits this change, and pushes it to the repository.
    *   Immediately after, it appends a "Keep alive 2" entry, commits this second change, and pushes it.
    *   Each commit uses specific messages: "chore: daily streak keep-alive (1/2)" and "chore: daily streak keep-alive (2/2)".

## Key Files

*   `.github/workflows/streak-keeper.yml`: This is the workflow definition file itself.
*   `streak_log.txt`: This file is updated by the workflow with date entries for each daily commit, serving as a log and a mechanism to prevent multiple commits on the same day.

## Setup and Maintenance

*   **No Local Machine Required:** Your local PC does not need to be powered on for this workflow to run. It executes entirely on GitHub's servers.
*   **Local Updates:** Since the commits are made remotely, you'll need to run `git pull` in your local `Personal-Only` repository to synchronize these changes with your local copy.
*   **Manual Trigger:** You can manually trigger the workflow at any time from the "Actions" tab of your repository on GitHub, by selecting the "Daily Streak Keeper" workflow and clicking "Run workflow".
*   **Customization:**
    *   To adjust the scheduled times, modify the `cron:` entry in `.github/workflows/streak-keeper.yml`.
    *   To change the number of commits or their messages, edit the `run:` section within the `Update Streak Log and Commit` step.
    *   To modify the randomization probability or sleep delay, adjust the bash logic within the `run:` section.
