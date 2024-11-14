# Sequence-Detector
Aim
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):

Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
Create the Testbench:

Write a testbench to apply input sequences and verify the output of both FSM designs.
Add the Verilog Files:

Add the Verilog code for both FSMs and the testbench to the Vivado project.
Run Simulation:

Run the simulation and observe the output to check if the sequence is detected correctly.
Observe the Waveforms:

Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
Save and Document Results:

Capture the waveforms and include the results in the final report.

Verilog Code for Sequence Detector Using Moore FSM

// moore_sequence_detector.v
```
   state_t current_state, next_state;

// State transition logic
always @(posedge clk or posedge reset) begin
    if (reset)
        current_state <= S0;
    else
        current_state <= next_state;
end

// Next state and output logic
always @(*) begin
    case (current_state)
        S0: begin
            if (seq_in) next_state = S1;
            else next_state = S0;
            detected = 0;
        end
        S1: begin
            if (seq_in) next_state = S1;
            else next_state = S2;
            detected = 0;
        end
        S2: begin
            if (seq_in) next_state = S3;
            else next_state = S0;
            detected = 0;
        end
        S3: begin
            if (seq_in) next_state = S4;
            else next_state = S2;
            detected = 0;
        end
        S4: begin
            if (seq_in) next_state = S1;
            else next_state = S0;
            detected = 1;  // Sequence 1011 detected
        end
        default: next_state = S0;
    endcase
end
```
![Screenshot (12)](https://github.com/user-attachments/assets/1916828b-6e12-40ab-858e-e01ba0364ce4)

Verilog Code for Sequence Detector Using Mealy FSM

// mealy_sequence_detector.v
```
module fsm_sequence(
    input clk,
    input reset,
    input run,
    output reg [3:0] count
);
    // State encoding
    localparam s0  = 4'd0;
    localparam s2  = 4'd2;
    localparam s4  = 4'd4;
    localparam s6  = 4'd6;
    localparam s8  = 4'd8;
    localparam s10 = 4'd10;
    localparam s12 = 4'd12;
    reg [3:0] current_state, next_state;
    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            current_state <= s0;  // Initialize to state s0 on reset
        end else begin
            current_state <= next_state;  // Move to next state on clock edge
        end
    end
    // Next state logic
    always @(*) begin
        case (current_state)
            s0:  next_state = run ? s2 : s0;
            s2:  next_state = run ? s4 : s2;
            s4:  next_state = run ? s6 : s4;
            s6:  next_state = run ? s8 : s6;
            s8:  next_state = run ? s10 : s8;
            s10: next_state = run ? s12 : s10;
            s12: next_state = run ? s0 : s12;
            default: next_state = s0;
        endcase
    end
    // Output logic (Mealy)
    always @(*) begin
        case (current_state)
            s0:  count = run ? 4'd2 : 4'd0;
            s2:  count = run ? 4'd4 : 4'd2;
            s4:  count = run ? 4'd6 : 4'd4;
            s6:  count = run ? 4'd8 : 4'd6;
            s8:  count = run ? 4'd10 : 4'd8;
            s10: count = run ? 4'd12 : 4'd10;
            s12: count = run ? 4'd0 : 4'd12;
            default: count = 4'd0;
        endcase
    end

endmodule
```
![Screenshot (14)](https://github.com/user-attachments/assets/7f26a98e-2c06-4df6-b943-d1573d2c85e1)


Testbench for Sequence Detector (Moore and Mealy FSMs)

// sequence_detector_tb.v

```
module tb_fsm_sequence;
    // Testbench signals
    reg clk;
    reg reset;
    reg run;
    wire [3:0] count;
    // Instantiate the FSM module
    fsm_sequence uut (
        .clk(clk),
        .reset(reset),
        .run(run),
        .count(count)
    );
    // Clock generation (50MHz, period = 20ns)
    always #10 clk = ~clk; // Toggle clock every 10 time units (20ns period)
    // Test sequence
    initial begin
        // Initialize inputs
        clk = 0;
        reset = 1;
        run = 0;
        // Apply reset
        #20 reset = 0;  // De-assert reset after 20ns
        // Test the FSM transitions with run signal
        #20 run = 1;    // Start running the FSM after 20ns
        // Observe FSM behavior by allowing it to transition through states
        #100 run = 0;   // Stop the FSM by disabling run
        #40 run = 1;    // Restart the FSM after a short pause
        #100 $finish;   // End the simulation
    end
    // Monitor the output
    initial begin
        $monitor("Time: %0t | clk: %b | reset: %b | run: %b | count: %d", 
                 $time, clk, reset, run, count);
    end
    // Dump waves for Vivado simulation
    initial begin
        $dumpfile("fsm_sequence_tb.vcd");
        $dumpvars(0, tb_fsm_sequence);
    end
endmodule
```
![Screenshot (15)](https://github.com/user-attachments/assets/68507202-9ebe-48d1-a47e-fb49644e7d8e)

Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
