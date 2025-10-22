# gitfinity

Automated daily GitHub activity generator that creates 0-8 random commits per day to maintain your GitHub contribution graph.

## Features

- **Truly random timing**: Each of the 4 daily runs adds a 0-120 minute random delay, so commits never happen at predictable times
- **Natural spread**: Commits distributed across morning, late morning, afternoon, and evening windows
- **Smart commit distribution**: Uses S-curve algorithm where 1 commit per run is most likely, 0 and 2 are less likely
- **Realistic patterns**: Commits appear throughout the day at unpredictable intervals
- **Automatic log management**: Keeps only the last 100 lines to prevent file bloat
- **Fully automated**: Uses GitHub Actions (no server required, no costs)
- **Simple activity logging**: Tracks timestamps in activity.log

## How It Works

The program uses GitHub Actions workflows that run **4 times per day** with **randomized timing**:
- 🌅 Early morning: anywhere between 6:00-8:00 AM UTC
- ☕ Late morning: anywhere between 10:00 AM-12:00 PM UTC
- 🌞 Afternoon: anywhere between 2:00-4:00 PM UTC
- 🌆 Evening: anywhere between 6:00-8:00 PM UTC

Each run adds a random 0-120 minute delay before executing, ensuring commits happen at unpredictable times!

Each run then executes a Python script that:

1. Uses S-curve distribution to select 0-2 commits (favoring 1 commit)
2. For each commit:
   - Updates the `activity.log` file with a timestamp
   - Creates a git commit
3. Automatically truncates the log file to 100 lines to prevent bloat
4. Pushes all commits to GitHub

**Result:** 0-8 commits per day, naturally spread throughout 24 hours!

## Setup

1. **Clone this repository** to your GitHub account (or use this one)

2. **Enable GitHub Actions**:
   - Go to your repository on GitHub
   - Click on the "Actions" tab
   - If prompted, enable GitHub Actions for this repository

3. **Configure the schedule** (optional):
   - Edit `.github/workflows/daily-commit.yml`
   - Modify the cron schedule: `'0 10 * * *'` (currently set to 10:00 AM UTC)
   - You can use [crontab.guru](https://crontab.guru/) to help create your schedule

4. **Test it manually**:
   - Go to Actions tab → "Daily GitHub Activity" workflow
   - Click "Run workflow" → "Run workflow"
   - Watch it create commits!

## Usage

### Automated (GitHub Actions)

Once set up, the workflow runs automatically daily. No action needed!

### Manual Execution

You can run the script locally:

```bash
# Run with automatic push
python3 daily_commit.py

# Run without pushing (commits only)
python3 daily_commit.py --no-push
```

## Files

- `daily_commit.py` - Main Python script that creates commits
- `activity.log` - File that tracks all activity timestamps
- `.github/workflows/daily-commit.yml` - GitHub Actions workflow configuration

## Customization

### Change random delay window

Edit `.github/workflows/daily-commit.yml` line 22:
```bash
DELAY=$((RANDOM % 7200))  # 7200 seconds = 120 minutes = 2 hours
```
Change `7200` to adjust the randomization window:
- `3600` = 0-60 minutes (1 hour window)
- `7200` = 0-120 minutes (2 hour window) ⭐ Default
- `10800` = 0-180 minutes (3 hour window)

### Change workflow base times

Edit `.github/workflows/daily-commit.yml` lines 6-9:
```yaml
- cron: '0 6 * * *'   # Early morning base time
- cron: '0 10 * * *'  # Late morning base time
- cron: '0 14 * * *'  # Afternoon base time
- cron: '0 18 * * *'  # Evening base time
```
You can add/remove runs or change the base times. Use [crontab.guru](https://crontab.guru/) for help.

### Change commit distribution per run

Edit `daily_commit.py` around line 30 in the `get_s_curve_commits()` method:
```python
weights = [20, 50, 30]  # 0, 1, 2 commits per run
```
Higher numbers = more likely. Example: `[10, 80, 10]` makes 1 commit much more likely.

### Change maximum commits per run

To change the range (currently 0-2), edit line 33:
```python
commits = random.choices(range(3), weights=weights, k=1)[0]
```
Change `range(3)` to `range(4)` for 0-3, and update weights accordingly.

### Change log file size limit

Edit `daily_commit.py` around line 74:
```python
self.truncate_log_file(max_lines=100)  # Change max_lines value
```

### Change commit message format

Edit `daily_commit.py` around line 86:
```python
commit_message = f"Daily activity update #{commit_number} - {timestamp}"
```

## Probability Distribution

With the default settings:

**Per run (0-2 commits):**
- 0 commits: 20% chance
- 1 commit: 50% chance ⭐ Most likely
- 2 commits: 30% chance

**Per day (4 runs):**
- Expected average: ~4-5 commits per day
- Minimum: 0 commits (all runs skip)
- Maximum: 8 commits (all runs create 2)
- Timing: Random within 4 different 2-hour windows throughout the day

**Example day:**
```
6:47 AM  → 1 commit
11:23 AM → 2 commits
2:08 PM  → 0 commits (skipped)
7:34 PM  → 1 commit

Total: 4 commits at unpredictable times!
```

## Requirements

- Python 3.x (included in GitHub Actions)
- Git (included in GitHub Actions)
- A GitHub repository with Actions enabled

## License

MIT

## Notes

This is meant for maintaining GitHub activity and learning automation. The commits are real and will appear in your contribution graph.

**Realism features:**
- ✅ Commits happen at random times (not predictable patterns)
- ✅ Distributed across 4 different time windows per day
- ✅ Variable commit count using S-curve distribution
- ✅ Automatic log file management

The randomization ensures your activity looks natural and human-like!
