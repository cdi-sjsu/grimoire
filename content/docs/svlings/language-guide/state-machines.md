---
title: Controlling behavior with states
weight: 70
---

# Controlling behavior with states

A finite-state machine (FSM) remembers which situation it is in and uses current inputs to choose
what happens next. A robust RTL FSM separates memory from decision-making.

## The two-block pattern

First define and store the state:

```systemverilog
typedef enum logic [1:0] {
    IDLE,
    WORK,
    FINISH
} state_t;

state_t state, next_state;

always_ff @(posedge clk or posedge reset) begin
    if (reset)
        state <= IDLE;
    else
        state <= next_state;
end
```

Then calculate the next state and combinational outputs:

```systemverilog
always_comb begin
    next_state = state;
    busy = 1'b0;
    done = 1'b0;

    case (state)
        IDLE: begin
            if (start)
                next_state = WORK;
        end

        WORK: begin
            busy = 1'b1;
            if (finished)
                next_state = FINISH;
        end

        FINISH: begin
            done = 1'b1;
            next_state = IDLE;
        end

        default: next_state = IDLE;
    endcase
end
```

The default `next_state = state` means stay where you are unless a transition says otherwise. The
output defaults ensure every output is assigned on every path, preventing latches.

## Reading the pattern

- The `always_ff` block is the only place that changes `state`.
- The `always_comb` block never changes `state`; it proposes `next_state`.
- On the next clock edge, the proposal becomes the current state.
- Outputs such as `busy` and `done` above depend only on state, a Moore-style output.
- An output that also depends directly on an input is often called Mealy-style.

## Designing an FSM from a description

1. List the distinct situations the circuit must remember.
2. Name those situations with an enum.
3. For every state, list the input conditions that cause transitions.
4. Decide which outputs are active in each state.
5. Choose a reset state that puts the design in a safe, known condition.
6. Check what happens for every state and every relevant input combination.

## Common mistakes

- Updating `state` in combinational logic instead of `next_state`.
- Using `=` in the state register or `<=` throughout next-state logic.
- Omitting defaults and accidentally creating latches.
- Letting two blocks drive the same output.
- Comparing enum state to unexplained numeric literals instead of enum members.
