```
FUNCTION_BLOCK MODBUS_SERVER
VAR_INPUT
    MAX_CONNECTIONS : UINT := 10;
    MODBUS_SERVER_IP : STRING := "YOUR_IP_ADDRESS";
    MODBUS_SERVER_PORT : UINT := 502;
VAR_OUTPUT
    CONNECTIONS_ACTIVE : ARRAY [0..9] OF BOOLEAN;
    ERROR_OCCURRED : BOOLEAN;
    LOG : ARRAY [0..100] OF STRING;
VAR
    CONNECTIONS : ARRAY [0..9] OF STRUCT
        CLIENT_IP : STRING;
        FUNCTION_CODE : UINT;
        DATA : ANY;
        RESPONSE_READY : BOOLEAN;
    END_STRUCT;
    COILS : ARRAY [0..1023] OF BOOLEAN;
    DISCRETE_INPUTS : ARRAY [0..1023] OF BOOLEAN;
    HOLDING_REGISTERS : ARRAY [0..32767] OF UINT;
    INPUT_REGISTERS : ARRAY [0..32767] OF UINT;
    BUFFER : BYTE_STRING;
    BUFFER_SIZE : UINT;
    BUFFER_POS : UINT;
    SOCKET : ANY; // Placeholder for socket object
END_VAR

// Initialize server socket
SOCKET := INIT_MODBUS_SERVER(MODBUS_SERVER_IP, MODBUS_SERVER_PORT);

// Main loop to handle connections
WHILE TRUE DO
    FOR i := 0 TO MAX_CONNECTIONS - 1 DO
        IF NOT CONNECTIONS_ACTIVE[i] THEN
            // Accept a new connection
            CONNECTIONS[i].CLIENT_IP := ACCEPT_CONNECTION(SOCKET);
            CONNECTIONS_ACTIVE[i] := TRUE;
        END_IF
        
        IF CONNECTIONS_ACTIVE[i] THEN
            BUFFER_SIZE := RECEIVE_DATA(SOCKET, BUFFER);
            BUFFER_POS := 0;
            
            // Parse request and get function code
            CONNECTIONS[i].FUNCTION_CODE := PARSE_FUNCTION_CODE(BUFFER, BUFFER_POS);
            
            // Execute Modbus function based on function code
            CASE CONNECTIONS[i].FUNCTION_CODE OF
                0x01: // Read Coils
                    EXECUTE_READ_COILS(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x02: // Read Discrete Inputs
                    EXECUTE_READ_DISCRETE_INPUTS(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x03: // Read Holding Registers
                    EXECUTE_READ_HOLDING_REGISTERS(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x04: // Read Input Registers
                    EXECUTE_READ_INPUT_REGISTERS(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x05: // Write Single Coil
                    EXECUTE_WRITE_SINGLE_COIL(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x06: // Write Single Register
                    EXECUTE_WRITE_SINGLE_REGISTER(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x0F: // Write Multiple Coils
                    EXECUTE_WRITE_MULTIPLE_COILS(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x10: // Write Multiple Registers
                    EXECUTE_WRITE_MULTIPLE_REGISTERS(CONNECTIONS[i], BUFFER, BUFFER_POS);
                0x17: // Read/Write Multiple Registers
                    EXECUTE_READ_WRITE_MULTIPLE_REGISTERS(CONNECTIONS[i], BUFFER, BUFFER_POS);
            END_CASE
            
            // Send response if ready
            IF CONNECTIONS[i].RESPONSE_READY THEN
                SEND_RESPONSE(SOCKET, CONNECTIONS[i].DATA);
                CONNECTIONS_ACTIVE[i] := FALSE;
            END_IF
        END_IF
    END_FOR
END_WHILE

// Example pseudo-functions for demonstration
PROCEDURE INIT_MODBUS_SERVER(IP : STRING; PORT : UINT)
    // Initializes the server with the given IP and port
END_PROC

PROCEDURE ACCEPT_CONNECTION(SOCKET : ANY) : STRING
    // Accepts a new connection and returns the client IP
    RETURN "CLIENT_IP";
END_PROC

PROCEDURE RECEIVE_DATA(SOCKET : ANY; BUFFER : REFERENCE TO BYTE_STRING) : UINT
    // Receives data from the client and stores it in BUFFER
    RETURN BUFFER_SIZE;
END_PROC

PROCEDURE PARSE_FUNCTION_CODE(BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT) : UINT
    // Parses the function code from the buffer
    RETURN FUNCTION_CODE;
END_PROC

PROCEDURE EXECUTE_READ_COILS(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Read Coils function
END_PROC

PROCEDURE EXECUTE_READ_DISCRETE_INPUTS(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Read Discrete Inputs function
END_PROC

PROCEDURE EXECUTE_READ_HOLDING_REGISTERS(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Read Holding Registers function
END_PROC

PROCEDURE EXECUTE_READ_INPUT_REGISTERS(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Read Input Registers function
END_PROC

PROCEDURE EXECUTE_WRITE_SINGLE_COIL(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Write Single Coil function
END_PROC

PROCEDURE EXECUTE_WRITE_SINGLE_REGISTER(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Write Single Register function
END_PROC

PROCEDURE EXECUTE_WRITE_MULTIPLE_COILS(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Write Multiple Coils function
END_PROC

PROCEDURE EXECUTE_WRITE_MULTIPLE_REGISTERS(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Write Multiple Registers function
END_PROC

PROCEDURE EXECUTE_READ_WRITE_MULTIPLE_REGISTERS(CONNECTION : REFERENCE TO STRUCT; BUFFER : BYTE_STRING; BUFFER_POS : REFERENCE TO UINT)
    // Executes the Read/Write Multiple Registers function
END_PROC

PROCEDURE SEND_RESPONSE(SOCKET : ANY; RESPONSE : ANY)
    // Sends the response back to the client
END_PROC
END_FUNCTION_BLOCK
```