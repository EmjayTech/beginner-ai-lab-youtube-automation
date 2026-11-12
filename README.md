# AI YouTube Automation System: Beginner AI Lab

An n8n + Google Sheets + Gemini pipeline that takes a YouTube channel from topic ideas to publish-ready packages and performance analysis.
Built as the capstone of the Big Brains Learning AI Automation Internship (mentor: Shoaib Hamza). Author: Jamiu Sanni.

> **Fictional channel.** "Beginner AI Lab" is a demo channel. **All performance data is simulated and labelled as such.**

## Architecture
```
Ideas -> Research -> Script -> Publishing Data -> Thumbnail Plan -> Content Calendar -> Performance -> AI Analytics Report
                                  (each stage writes to a Google Sheet; Error Handler logs + emails failures)
```
Design principles:
- Code computes all metrics; the AI only interprets them.
- Prompts say "use only provided data" and mark unverified claims with `[VERIFY]`.
- Every recommendation carries an evidence field.
- Bulk workflows are idempotent: they skip Video IDs already processed.
- Manual statuses (Production, Published, Failed) are protected from automation.

## Workflows (`workflows/`)
| File | Purpose |
|---|---|
| 07-content-calendar-automation | Sets Script/Thumbnail/Video status, assigns publish dates, emails when a video becomes Ready to Publish |
| 08-ai-performance-analyzer | Calculates metrics, asks Gemini for insights, saves a report |
| 09-complete-pipeline | Master pipeline: ideas through performance, one run |
| 10-bulk-scripts | Scripts for all researched videos |
| 11-bulk-publishing-data | Titles, description, tags, chapters |
| 12-bulk-thumbnails | Thumbnail concepts and image prompts |
| 13-bulk-content-calendar | Builds/updates calendar rows from script and thumbnail status |
| 14-performance-data | Simulated metrics for ready/published videos |
| 15-error-handler | Error Trigger: logs to the Error Log sheet and sends an email alert |

## Tools
n8n (Docker, localhost:5678), Google Sheets, Gemini (`models/gemini-3.8-flash`, `models/gemini-3-flash-preview`), Gmail.

## How to run
1. Start n8n in Docker and open http://localhost:5678.
2. Create the sheet tabs from `docs/SHEETS.md`.
3. Import each JSON (Workflows > Import from file) and reconnect your Google Sheets, Gemini and Gmail credentials.
4. Replace the spreadsheet ID and alert email in the JSONs with your own.
5. Run order: Ideas > Research > 10 > 11 > 12 > 13 > 07 > 14 > 08. Or run 09 alone.
6. In each workflow's Settings, set **Error Workflow** to the Error Handler.

## Test results
| Stage | Result |
|---|---|
| Ideas | 30 ideas generated |
| Research | 10 videos researched |
| Scripts | 10 scripts |
| Publishing Data | 10 rows |
| Thumbnails | 10 of 10 completed (Gemini free-tier quota) |
| Calendar | Status rules tested (Script Required, Thumbnail Required, Ready to Publish); duplicate emails prevented |
| Performance | 4 videos with simulated data |
| Analytics | Report generated on 17 videos |
| Error handling | Proven with n8n sample data: Error Log row + FAILED email |

## Known limitations
- Gemini free tier: 20 requests/day/model and 5/min, so large batches stall (429) or hit overload (503). Wait for reset or use billing.
- Thumbnail images are not generated; the system produces concepts and prompts, and a human adds the Thumbnail URL.
- Performance data is simulated, not from the YouTube API.
- Error Workflow fires only on non-manual executions.
