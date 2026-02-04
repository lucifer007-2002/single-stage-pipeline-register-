
# Single-Stage Pipeline Register with Valid/Ready Handshake

A synthesizable SystemVerilog implementation of a single-stage pipeline register using industry-standard valid/ready handshake protocol.

## Features

- **Valid/Ready Handshake**: Full AXI-style ready/valid signaling
- **Backpressure Support**: Correctly stalls when downstream isn't ready
- **Parameterized Data Width**: Configurable via `DATA_WIDTH` parameter
- **Zero Data Loss**: Guarantees no dropped or duplicated transfers
- **Fully Synthesizable**: Clean RTL design suitable for FPGA/ASIC
- **Asynchronous Reset**: Active-low reset for clean initialization

## Protocol

### Input Interface
- `in_valid`: Source asserts when data is available
- `in_ready`: Module asserts when it can accept data
- `in_data`: Data payload
- **Transfer occurs**: `in_valid && in_ready` on rising clock edge

### Output Interface
- `out_valid`: Module asserts when data is available
- `out_ready`: Sink asserts when it can accept data
- `out_data`: Registered data payload
- **Transfer occurs**: `out_valid && out_ready` on rising clock edge

## Design Details

The pipeline register contains:
- **Data Register**: Stores the data payload
- **Valid Register**: Tracks whether the register contains valid data

**Key Behavior**:
- `in_ready` is HIGH when register is empty OR output is consuming data
- Data latched on rising edge when `in_valid && in_ready`
- `out_valid` tracks the valid bit of stored data
- On backpressure (`!out_ready`), input stalls and data is preserved

## Directory Structure
```
pipeline-register-sv/
├── README.md              # This file
├── rtl/
│   └── pipeline_register.sv    # Main design module
├── tb/
│   └── tb_pipeline_register.sv # Testbench
└── sim/
    └── run.do             # Simulation script
```

## Running the Testbench

### Using QuestaSim/ModelSim:
```bash
cd sim
vsim -do run.do
```

### Using other simulators:
Compile both `rtl/pipeline_register.sv` and `tb/tb_pipeline_register.sv`, then simulate `tb_pipeline_register`.

## Test Coverage

The testbench verifies:
1. **Basic Transfer**: Single data transfer with ready receiver
2. **Backpressure Handling**: Input stalls when output can't accept
3. **Burst Transfers**: Back-to-back data items
4. **Random Stalls**: Randomized valid/ready signals
5. **Reset Behavior**: Clean state on reset assertion

## Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `DATA_WIDTH` | 32 | Width of data payload in bits |

## Synthesis

This design is fully synthesizable. It uses:
- Standard flip-flops for data and valid storage
- Combinational logic for ready generation
- Asynchronous active-low reset

No vendor-specific primitives or constructs are used.

## Timing

Single-cycle latency from input to output when pipeline is empty.

## Author

Created as a technical assessment submission.

**I've used Claude for Github related work in this repository**
