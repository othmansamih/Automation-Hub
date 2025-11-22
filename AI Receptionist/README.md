# 🤖 AI Receptionist - Al Mada Dental Clinic

An intelligent voice assistant that handles appointment scheduling for Al Mada Dental Clinic using ElevenLabs conversational AI and n8n workflow automation.

## 📋 Overview

This AI receptionist automatically:
- Greets callers professionally
- Checks appointment availability in real-time
- Books appointments by collecting patient information
- Syncs with Google Calendar
- Provides a human-like conversation experience

## 🏗️ Architecture

```
ElevenLabs AI Agent (Adam)
         ↓
    Webhook Endpoints (n8n)
         ↓
   Google Calendar API
```

### Components:
1. **ElevenLabs Voice Agent** - Conversational AI interface
2. **n8n Workflow** - Backend automation and API orchestration
3. **Google Calendar** - Appointment management system

## 🚀 Getting Started

### Prerequisites
- n8n instance (self-hosted or cloud)
- ElevenLabs account with conversational AI access
- Google Calendar API credentials
- Public URL for webhook endpoints

### Installation

#### Step 1: Deploy n8n Workflow

1. Import the workflow JSON file into your n8n instance
2. Configure Google Calendar credentials:
   - Go to the "Get Booked Events" node
   - Add your Google Calendar OAuth2 credentials
   - Select your calendar (e.g., `agentrius@gmail.com`)
3. Do the same for the "Book an Appointment" node
4. Activate the workflow
5. Note your webhook URLs:
   - Availability check: `https://your-n8n-domain/webhook/availability`
   - Booking: `https://your-n8n-domain/webhook/book`

#### Step 2: Configure ElevenLabs Agent

1. Create a new conversational agent in ElevenLabs
2. Set the agent name: **AL Mada Assistant**
3. Configure the agent:

**First Message:**
```
Good afternoon. You've reached Al Mada Dental Clinic. My name is Adam. How can I help you?
```

**System Prompt:** (See `system-prompt.md` in the assets folder)

**Language:** Select your preferred language (e.g., French, English)

**Voice:** Choose an appropriate professional voice

**LLM:** Select your preferred language model

#### Step 3: Add Tools to ElevenLabs Agent

##### Tool 1: Get_Available_Slots

**Name:** `Get_Available_Slots`

**Description:**
```
Use this tool to check available appointment slots for the requested date. 
Call this tool whenever the caller asks about availability or wants to know open times.
```

**Configuration:**
- **Method:** POST
- **URL:** `https://your-n8n-domain/webhook/availability`
- **Body Parameters:**
  - `date` (string) - Requested appointment date

**Parameter Description:**
```
Extract the appointment date from the caller's message (e.g., tomorrow, next Monday, 15 March). 
If the caller did not provide a date, ask them politely to specify one.
```

##### Tool 2: Book_Meeting

**Name:** `Book_Meeting`

**Description:**
```
Use this tool to book the patient's appointment after they choose an available slot 
and provide their personal information. Call this tool only after collecting 
full name, date of birth, and phone number.
```

**Configuration:**
- **Method:** POST
- **URL:** `https://your-n8n-domain/webhook/book`
- **Body Parameters:**
  - `startTime` (string) - The beginning of the available appointment slot
  - `endTime` (string) - The end of the available appointment slot
  - `full_name` (string) - Patient's full name
  - `date_of_birth` (string) - User's birth date in YYYY-MM-DD format
  - `phone` (string) - Patient's phone number

**Parameter Description:**
```
- Ask the caller for their full name and capture it exactly as spoken.
- Ask the caller for their date of birth and record it exactly as provided.
- Collect the caller's phone number, extracting only the digits and ignoring any filler words.
- Use the startTime and endTime values from the list returned by the Get_Available_Slots tool.
```

## 🛠️ Workflow Details

### Availability Check Flow

1. **Webhook receives request** with date parameter
2. **Get Booked Events** queries Google Calendar for existing appointments (9 AM - 6 PM)
3. **Get Available Events** (JavaScript Code node):
   - Creates 30-minute time slots from 9 AM to 6 PM
   - Filters out occupied slots
   - Returns available time slots with formatted times
4. **Responds** with available slots in JSON format

### Booking Flow

1. **Webhook receives** patient information and selected time slot
2. **Book an Appointment** creates Google Calendar event with:
   - Patient name as summary
   - Full patient details (name, DOB, phone, reason) in description
   - Location set to "AL Mada Clinic - Casablanca"
   - Color coded (blue - color ID: 2)
3. **Responds** with booking confirmation

## 📝 Configuration Options

### Clinic Hours
Edit in the "Get Available Events" node:
```javascript
const startHour = 9;  // 9 AM
const endHour = 18;   // 6 PM
const slotDuration = 30; // minutes
```

### Time Zone
Currently set to `Europe/Paris`. Modify in:
- "Get Booked Events" node: `timeMin` and `timeMax` parameters
- "Get Available Events" node: timezone in date parsing

### Appointment Duration
Default: 30 minutes. Change `slotDuration` in the JavaScript code node.

### Calendar Location
Update in "Book an Appointment" node:
```
location: "AL Mada Clinic - Casablanca"
```

## 🔒 Security Considerations

- Webhook endpoints should be secured with HTTPS
- Consider adding authentication to webhooks
- Protect Google Calendar API credentials
- Implement rate limiting on webhook endpoints
- Validate all input data in the workflow
- Store sensitive patient information securely

## 🧪 Testing

### Test Availability Check
```bash
curl -X POST https://your-n8n-domain/webhook/availability \
  -H "Content-Type: application/json" \
  -d '{"date": "2025-11-23"}'
```

**Expected Response:**
```json
[
  {
    "date": "2025-11-23",
    "startTime": "2025-11-23T09:00:00.000Z",
    "endTime": "2025-11-23T09:30:00.000Z",
    "startTimeFormatted": "09:00",
    "endTimeFormatted": "09:30"
  }
]
```

### Test Booking
```bash
curl -X POST https://your-n8n-domain/webhook/book \
  -H "Content-Type: application/json" \
  -d '{
    "startTime": "2025-11-23T14:00:00+01:00",
    "endTime": "2025-11-23T14:30:00+01:00",
    "full_name": "John Doe",
    "date_of_birth": "1990-05-15",
    "phone": "0612345678",
    "reason": "Regular checkup"
  }'
```

## 📊 Example Conversation Flow

**Assistant:** Good afternoon. You've reached Al Mada Dental Clinic. My name is Adam. How can I help you?

**Caller:** Hi, I'd like to book an appointment for tomorrow.

**Assistant:** Please allow me a moment to check that for you.  
[*Calls Get_Available_Slots tool*]  
We have several openings tomorrow. Would you prefer morning or afternoon?

**Caller:** Afternoon, please.

**Assistant:** For tomorrow afternoon, we have slots at 3:00 PM and 4:30 PM. Which works better for you?

**Caller:** 3:00 PM works great.

**Assistant:** Perfect. I'll book you for tomorrow at 3:00 PM. May I have your full name, please?

**Caller:** It's Sarah Martinez.

**Assistant:** Thank you, Sarah. And could you provide your date of birth?

**Caller:** June 15, 1985.

**Assistant:** Thank you. And your phone number, please?

**Caller:** 06 12 34 56 78.

**Assistant:** Please hold on while I book your appointment.  
[*Calls Book_Meeting tool*]  
Your appointment is confirmed for tomorrow at 3:00 PM. We look forward to seeing you at Al Mada Dental Clinic. Is there anything else I can help you with?

**Caller:** No, that's all. Thank you!

**Assistant:** You're very welcome, Sarah. Have a great day!

## 🎯 Key Features

### Intelligent Scheduling
- Automatically detects business hours
- Prevents double-booking
- Real-time availability checks
- Handles same-day booking restrictions

### Natural Conversation
- Context-aware responses
- Handles various date formats
- Adapts to caller preferences
- Professional yet friendly tone

### Data Collection
- Structured patient information gathering
- Validates required fields
- Formats data for calendar events
- Includes appointment reasons

## 🐛 Troubleshooting

### No Available Slots Returned
- Check Google Calendar permissions
- Verify timezone settings match your location
- Ensure working hours are correctly configured
- Confirm calendar has actual availability

### Booking Fails
- Verify all required fields are provided
- Check start/end times are in correct format
- Ensure calendar write permissions are granted
- Validate timezone in datetime strings

### Webhook Not Responding
- Confirm n8n workflow is activated
- Check webhook URLs are correct in ElevenLabs
- Verify your n8n instance is publicly accessible
- Review n8n execution logs for errors

## 🔄 Future Enhancements

Potential improvements to consider:
- Multi-language support
- SMS confirmation messages
- Email notifications
- Cancellation/rescheduling capabilities
- Integration with patient management systems
- Analytics and reporting dashboard
- Multiple provider/room support
- Waiting list management

## 📄 License

This project is provided as-is for educational and commercial use. Modify as needed for your specific requirements.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs and feature requests.

## 📞 Support

For questions or support:
- Open an issue in this repository
- Check n8n documentation: https://docs.n8n.io
- Check ElevenLabs documentation: https://elevenlabs.io/docs

## 🙏 Acknowledgments

- Built with [n8n](https://n8n.io)
- Voice AI powered by [ElevenLabs](https://elevenlabs.io)
- Calendar integration via [Google Calendar API](https://developers.google.com/calendar)

---

Made with ❤️ to automate the boring stuff.