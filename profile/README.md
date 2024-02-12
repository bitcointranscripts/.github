## Bitcoin Transcripts Pipeline

```mermaid
sequenceDiagram
    actor Curator
    participant tstbtc
    actor Evaluator
    participant bitcointranscripts
    participant Site as btctranscripts.com
    participant backend as transcription-review-backend
    participant discord as Discord Server
    participant GitHub
    participant frontend as transcription-review-front-end
    actor Reviewer
    
    Note over Curator,tstbtc: Transcript Creation Process
    opt transcription using tstbtc
        Curator->>+tstbtc: Submits source for transcription
        tstbtc-->>-Curator: Returns AI-generated transcript
    end
    Note over Curator,discord: Queueing Process
    Curator->>bitcointranscripts: Commits AI-generated transcript
    bitcointranscripts->>+backend: Triggers webhook for new content push
    backend->>backend: Adds new transcript to the Queue
    alt success
        backend-->>discord: Alerts users of new reviewable transcript
    else error
        backend-->>-discord: Alerts developers for error
    end

    Note right of bitcointranscripts: Repository Update
    bitcointranscripts->>Site: Triggers website rebuildte with new content

    Site->>Site: Rebuild with updated content

    Note over bitcointranscripts,Reviewer: Review Process
    Reviewer->>+frontend: Accesses review platform
    frontend->>+GitHub: Requests GitHub login
    GitHub-->>-frontend: Confirms authentication
    frontend-->>-Reviewer: Grants access to platform

    Reviewer->>+frontend: Requests transcripts list
    frontend->>+backend: Queries for available transcripts
    backend-->>-frontend: Returns available transcripts
    frontend->>-Reviewer: Displays transcripts

    Reviewer->>+frontend: Claims a transcript
    alt First-Time Claim
        frontend->>+GitHub: Fork bitcointranscripts repo
        GitHub-->>-frontend: Confirms repository fork
    end
    frontend->>+GitHub: Creates working branch
    GitHub-->>-frontend: Confirms branch creation
    frontend->>+backend: Registers transcript claim
    backend-->>-frontend: Return assigned review ID
    frontend->>-Reviewer: Shows claimed transcript for editing

    loop Edit Transcript
        Reviewer->>+frontend: Save edits
        frontend->>+GitHub: Commit edits to branch
        GitHub-->>-frontend: Confirms commit
        frontend-->>-Reviewer: Save confirmation
    end
    
    Reviewer->>+frontend: Submits Review
    frontend->>+GitHub: Initiates Pull Request
    GitHub->>+bitcointranscripts: Opens PR to bitcointranscripts repo
    GitHub-->>-frontend: PR creation confirmation
    frontend->>+backend: Notifies backend of submission
    backend-->>-frontend: Acknowledges submission
    frontend-->>-Reviewer: Confirms submission

    Note over Evaluator,Reviewer: Evaluation Process
    loop PR Evaluation
        Evaluator->>+bitcointranscripts: Reviews PR
        alt Comments
            bitcointranscripts-->>Reviewer: Requests change
            loop Edit Transcript
                Reviewer->>+frontend: Save edits
                frontend->>+GitHub: Commit edits to branch
                GitHub-->>-frontend: Confirms commit
                frontend-->>-Reviewer: Save confirmation
            end
        else Accept
            bitcointranscripts-->>Reviewer: Approves PR
        end
    end

    Evaluator->>bitcointranscripts: Merges PR
    bitcointranscripts->>backend: Triggers webhook for PR merge
    backend->>backend: Calculate payout and credit to Reviewer's account

    Note right of bitcointranscripts: Repository Update
    bitcointranscripts->>Site: Triggers website rebuild
    Site->>Site: Rebuild with updated content

```

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->
