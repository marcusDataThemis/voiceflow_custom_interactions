# voiceflow_custom_interactions
Demo the ability to integrate custom interactions into a voiceflow chatbot


https://marcusdatathemis.github.io/voiceflow_custom_interactions/

calendar: https://calendar.google.com/calendar/u/2/r/week

```mermaid
sequenceDiagram
    actor User
    participant ClientWebsite
    participant CloudflareWorkers
    participant CustomVFUIElement
    participant VoiceflowServer

    User ->> ClientWebsite: Visits
       note right of ClientWebsite: Load UI
    rect rgb(191, 223, 255) 

    ClientWebsite ->> CloudflareWorkers: Request
    CloudflareWorkers ->> CloudflareWorkers: Retrieve React UI Chat Bot Bundle

    CloudflareWorkers -->> ClientWebsite: deliver React UI code 

    ClientWebsite ->> CustomVFUIElement: Loads Custom VF UI Element
    CustomVFUIElement ->> VoiceflowServer: Authenticate with Project ID
    VoiceflowServer ->> CustomVFUIElement: Successful Authentication
    CustomVFUIElement -->> ClientWebsite: Render
    end
      loop
    User -->> CustomVFUIElement: send message to bot
    CustomVFUIElement -->> User : receive reply
    end
note right of ClientWebsite: Scheduling
rect rgb(191, 223, 255) 
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
    CloudflareWorkers->> GoogleCalendarAPI: Authenticate with Service Account Token
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
end
```

Diagram created and editable via: https://mermaid.live/edit
