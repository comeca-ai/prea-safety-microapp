flowchart TD
  A[Scan QR Code] --> B[Sign In via Magic Link]
  B --> C[Dashboard]
  C --> D[Start Safety Quiz]
  D --> E{All questions answered}
  E -->|No| D
  E -->|Yes| F[Show Quiz Feedback]
  F --> G[Start Pre-session Checklist]
  G --> H{All items checked}
  H -->|No| G
  H -->|Yes| I[Submit Checklist]
  I --> J[Save Records to Supabase]
  J --> K{View History}
  K -->|Yes| L[History Page]
  K -->|No| M[End]