# voiceflow_custom_interactions
Demo the ability to integrate custom interactions into a voiceflow chatbot


https://marcusdatathemis.github.io/voiceflow_custom_interactions/

calendar: https://calendar.google.com/calendar/u/2/r/week

```
sequenceDiagram
    participant User
    participant ClientWebsite
    participant CloudflareWorkers
    participant CustomVFUIElement
    participant VoiceflowServer

    User ->> ClientWebsite: Visits
    ClientWebsite ->> CloudflareWorkers: Request
    CloudflareWorkers ->> CloudflareWorkers: Retrieve React UI Chat Bot Bundle
    note right of CloudflareWorkers: acting as a CDN to deliver bundle
    CloudflareWorkers -->> ClientWebsite: React UI

    ClientWebsite ->> CustomVFUIElement: Loads Custom VF UI Element
    CustomVFUIElement ->> VoiceflowServer: Authenticate with Project ID
    VoiceflowServer ->> CustomVFUIElement: Successful Authentication
    CustomVFUIElement -->> ClientWebsite: Render
    
    User <->> CustomVFUIElement: Interaction

    User ->> CustomVFUIElement: chat message requesting scheduling
    CustomVFUIElement ->> VoiceflowServer: fwd req to server
    VoiceflowServer ->> CustomVFUIElement: Sends custom 'Calendar' message
    CustomVFUIElement ->> CustomVFUIElement: Renders custom react calendar element in the chat
    
    User ->> CustomVFUIElement: Clicks Calendar Element, picking a day

    activate CustomVFUIElement
    User ->> CustomVFUIElement: chat message requesting scheduling
    CustomVFUIElement ->> VoiceflowServer: fwd req to server
    VoiceflowServer ->> CustomVFUIElement: Sends custom 'Calendar' message
    CustomVFUIElement ->> CustomVFUIElement: Renders custom react calendar element in the chat
    deactivate CustomVFUIElement
    
    User ->> CustomVFUIElement: Clicks Calendar Element, picking a day

    activate VoiceflowServer
    CustomVFUIElement ->> VoiceflowServer: Forward selected day to VF server
    activate CloudflareWorkers
    VoiceflowServer ->> CloudflareWorkers: Request for calendar free slots on the chosen day
    CloudflareWorkers ->> GoogleCalendarAPI: Authenticate with Service Account Token
    GoogleCalendarAPI -->> CloudflareWorkers: Responds with authentication result
    CloudflareWorkers ->> GoogleCalendarAPI: Request all free/busy info from Google Calendar
    GoogleCalendarAPI -->> CloudflareWorkers: Responds with free/busy Schedule
    CloudflareWorkers ->> CloudflareWorkers: Transform Schedule into free slots
    CloudflareWorkers -->> VoiceflowServer: Sends free slots as Response JSON
    deactivate CloudflareWorkers
    VoiceflowServer ->> VoiceflowServer: Converts to user-friendly Chatbot Response
    VoiceflowServer ->> CustomVFUIElement: Sends Chatbot Response
    deactivate VoiceflowServer

    User ->> CustomVFUIElement: Read Response & Proceed
```
