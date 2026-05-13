# mc68331-boot

Initial bootloader for MC68331 to receive the next stage from the internal serial peripheral.
It expects to be places at 0xFC0.
It expects 3007 bytes and writes them to 0x400 in RAM.
When finished it will return control to the debugger attached via BDM.

## Flowchart

```mermaid
graph TD
    A[Start] --> B[Initialize Serial Control]
    B --> C[Configure SCCR0 for baud rate]
    C --> D[Configure SCCR1 to enable Transmit and Receive]
    D --> E[Set up pointers for status and data registers]
    E --> F[Set target address in A2 to 0x400]
    F --> G[Clear D0 and D1]
    G --> H[Set D3 to 0xBBF counter]
    H --> I[Wait for RX full]
    I --> J[Check for errors in status]
    J --> K{No errors?}
    K -->|Yes| L[Store data from SCDR to target address]
    L --> M[Increment target address]
    M --> N[Decrement counter]
    N --> O{Counter == 0?}
    O -->|No| I
    O -->|Yes| P[Finish]
    K -->|No| P
    P --> Q[Reset serial]
    Q --> R[Hand back control with BGND]
```
