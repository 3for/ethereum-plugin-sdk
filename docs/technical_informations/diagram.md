This diagram showcases a complete sequence of the parsing, display, and signature of a smart contract.

```mermaid
sequenceDiagram
    participant L as Ledger Live
    participant T as TRON application
    participant SDK as Plugin SDK
    participant P as Plugin
    L->>T: SET_EXTERNAL_PLUGIN
    T->>+SDK: TRON_PLUGIN_CHECK_PRESENCE
    SDK->>-T: ack;
    L->>+T: Transaction to sign
    Note over T: The plugin for this smart<br/>contract exists, rely on it
    T->>+SDK: TRON_PLUGIN_INITIALIZE
    SDK->>+P: handle_init_contract()
    Note over P: Your code here
    P->>-SDK: return;
    SDK->>-T: ;
    Note over T: Split contract data in chunks
    loop Send smart contract chunks
        T->>+SDK: TRON_PLUGIN_PROVIDE_PARAMETER
        SDK->>+P: handle_provide_parameter()
        Note over P: Your code here
        P->>-SDK: return;
        SDK->>-T: ;
    end
    Note over T: Inform plugin that everything was sent
    T->>+SDK: TRON_PLUGIN_FINALIZE
    SDK->>+P: handle_finalize()
    Note over P: Your code here
    P->>-SDK: Number of screens needed<br/>Ask TRC20 tokens info if needed
    SDK->>-T: ;
    opt
        Note over L: Knows that TRON<br/>will need TRC20 token info
        L->>T: Provide TRC20 token
        T->>+SDK: TRON_PLUGIN_PROVIDE_TOKEN
        SDK->>+P: handle_provide_token()
        Note over P: Your code here
        P->>-SDK: Update screens number
        SDK->>-T: ;
    end
    Note over T: Ready to start display
    T->>+SDK: TRON_PLUGIN_QUERY_CONTRACT_ID
    SDK->>+P: handle_query_contract_id()
    Note over P: Your code here
    P->>-SDK: Provide title screen
    SDK->>-T: ;
    loop For every screen requested
        T->>+SDK: TRON_PLUGIN_QUERY_CONTRACT_UI
        SDK->>+P: handle_query_contract_ui()
        Note over P: Your code here
        P->>-SDK: Provide screen
        SDK->>-T: ;
    end
    Note over T: User validates or rejects
    T->>-L: Transaction signed / rejected
```
