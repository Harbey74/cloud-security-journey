 ## Architecture


 
                S3 Bucket
         skyshield-abiodun-2026
                     │
     ┌───────────────┼────────────────┐
     │               │                │
 Block Public   Default SSE-S3    Versioning
    Access        Encryption       Enabled
     │               │                │
     └───────────────┼────────────────┘
                     │
             Lifecycle Policy
                     │
       Day 0 ─────────► S3 Standard
                     │
             After 90 Days
                     ▼
              S3 Standard-IA
                     │
            After 365 Days
                     ▼
      Glacier Flexible Retrieval
                     │
             Retained Indefinitely