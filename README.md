# Code Snippet - Using Modbus RTU Master Function Block (TF6255)

## Disclaimer

This guide is a personal project and not a peer-reviewed publication or sponsored document. It is provided “as is,” without any warranties—express or implied—including, but not limited to, accuracy, completeness, reliability, or suitability for any purpose. The author(s) shall not be held liable for any errors, omissions, delays, or damages arising from the use or display of this information.

All opinions expressed are solely those of the author(s) and do not necessarily represent those of any organization, employer, or other entity. Any assumptions or conclusions presented are subject to revision or rethinking at any time.

Use of this information, code, or scripts provided is at your own risk. Readers are encouraged to independently verify facts. This content does not constitute professional advice, and no client or advisory relationship is formed through its use.

## Description

This is a collection of samples as code snippets to help understand how to use the Modbus RTU Master Function Blocks

The function block is called via Actions, therefore, in the action you pass in the usual values for the FB.

The input / output parameters have different meaning depending on what action they are called with. I.e. calling modbus.WriteRegs(...) vs modbus.WriteCoils(...) will treat the input variables differently.

See the table on [infosys](https://infosys.beckhoff.com/english.php?content=../content/1033/tf6255_tc3_modbus_rtu/13966844555.html) for the exact use.

## Code Snippets

### ModbusRtuMaster_PcCOM.WriteRegs

```
PROGRAM MAIN
VAR
	enableWriteRegs : BOOL;
	busy : BOOL;
	values : ARRAY [0..3] OF WORD;
	modbus : ModbusRtuMaster_PcCOM;
	stationId : BYTE := 1;
	destinationAddress : WORD := 0;
END_VAR


IF enableWriteRegs AND NOT busy THEN

	// update the values each time so you can see changes.
	values[0]:= values[0]+1;
	values[1]:= values[1]+1;
	values[2]:= values[2]+1;
	values[3]:= values[3]+1;

	//
	 // Trigger a Modbus Write Multiple Registers (function code 16)
    modbus.WriteRegs(
        UnitID := stationId, // [BYTE] Modbus station address (1..247). Must match the slave ID.
        Quantity := 4, // [WORD] Number of WORDs (registers) to write. You are writing 4 words.
        MBAddr := destinationAddress, // [WORD] Modbus data address on the slave to start writing to.
        cbLength := SIZEOF(values),	// [UINT] Number of BYTES to send.
        pMemoryAddr := ADR(values[0]), // [BYTE] Pointer to the data to send, starting at values[0].
        Execute := TRUE, // [BOOL] Start signal. The FB is triggered by a rising edge..
        Timeout := T#1S, // [TIME] Timeout for waiting for a slave response.
        Busy => busy // [BOOL] Indicates the FB is processing.
    );

ELSIF busy THEN

	modbus.WriteRegs(Execute:= FALSE);

END_IF

```
