# Gmail Automation Internship

An n8n workflow that reads internship related emails from Gmail, extracts the key details with a language model, and records them in Google Sheets as a single organized tracker.

## Overview

Managing internship applications through an inbox quickly becomes difficult. Interview invitations, assessment links, document requests, and rejections arrive from many sources and are easy to miss. This workflow monitors the inbox automatically, reads each relevant email, and logs the company, position, deadline, and schedule in one place.

## How It Works

1. A Gmail Trigger checks the inbox every 10 minutes for emails labeled Magang or Remote.
2. A duplicate filter uses the email ID so that each message is processed only once.
3. A JavaScript step removes quoted replies, limits the text to 3000 characters, and detects the source, which can be LinkedIn, Dealls, or a direct email.
4. A second JavaScript step builds the request, including a structured prompt, and prepares it for the model.
5. An HTTP Request node sends the request to a chat completions API and receives the classification.
6. A parsing step converts the model output into structured data and applies a fallback when the output is invalid.
7. A formatting step arranges the data into one row per email.
8. A Google Sheets node appends the row to a sheet named EmailLog.

## Extracted Information

Each email is assigned one of the following categories: interview, online assessment, document request, offering, rejection, process update, or not relevant.

The model also extracts the company, position, a short summary, the deadline, the schedule, special instructions, and any links mentioned in the email. When the content is unclear, the email is flagged for manual review together with the reason.

## Prompt Design

The prompt treats email content strictly as data and ignores any instruction written inside an email. The model may only use information found in the message and must return null when a detail is missing. The temperature is set to zero to keep results consistent, and the response must be valid JSON.

## Error Handling

If the API request fails or the response cannot be parsed, the email is still recorded with a review flag and an explanation. This ensures that no message is lost because of a temporary failure.

## Requirements

1. An n8n instance, either cloud or self hosted
2. A Gmail account with the labels Magang and Remote
3. A Google account with access to Google Sheets
4. An API key for a chat completions compatible model provider

## Setup

1. Download the workflow file from this repository and import it into n8n using the Import from file option.
2. Create the Gmail labels Magang and Remote and apply them to relevant emails, or adjust the search query in the Gmail Trigger node.
3. Create a Google Sheet with a tab named EmailLog. Add these column headers in the first row: email_id, tanggal, pengirim, subjek, sumber, kategori, perusahaan, posisi, ringkasan, deadline, jadwal, perlu_diperiksa, hasil_luna, hasil_gemini, benar_salah, and status_notifikasi.
4. Connect your Gmail and Google Sheets credentials in n8n and select your spreadsheet in the final node.
5. Create an HTTP Header Auth credential containing your API key and assign it to the classification node.
6. Update the API address and model name in the request nodes to match your provider.
7. Activate the workflow.

## Planned Features

The last three columns of the sheet are reserved for upcoming improvements. These include comparing results from a second model for accuracy checks, marking each result as correct or incorrect, and sending notifications for upcoming deadlines and interviews.

## Privacy

All credentials, spreadsheet identifiers, and personal data have been removed from the exported workflow. Each user must supply their own accounts and keys.

## Built With

n8n, Gmail API, Google Sheets API, JavaScript, and a large language model through an HTTP API.
