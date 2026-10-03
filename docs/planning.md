# Planning
## Initial Meeting Plans for MVP
### Design
We are creating a couple things. First, a language for specifying how to read
and write data following certain protocols. We want to separate the data being
sent from the protocol itself.

We additionally want to separate verification of the data we receive from
writing and reading the protocol. For example, if a protocol starts with a
header where the third bit in the header must be high, we likely wouldn't check
that online while reading. Supporting online checks for protocols is something
we'd like to offer but probably is a distraction to do right away.

Protocols will be split into frames. Users specify in an assembly language how
to read and write each frame and after reading one frame which frame to read
next. When reading frames, data is read into a shift register (or maybe some
other form of memory) and upon completion of reading the frame this register is
written to SRAM for further processing/analysis. A mask of this register may
also be passed to the next frame.

The instructions in the assembly language will be pretty simple and contain
roughly what you'd expect. There are instructions to read/write to a subset of
pins. There are possibly instructions to do basic binary operations. There are
instructions for in frame control flow e.g. while loops, if/else, and repeat
loops. And finally there are instructions for control flow between frames. In
some ways generating a value (by reading or writing to a register which later
gets persisted) and then calling a frame on that mask is kind of like
continuation passing style, but also not quite because frames don't really take
in a single continuation.

As high level example, reading UART might involve two frames. There is an "idle"
frame containing a while loop which continues reading (and discarding) bits
while the line is set high (UART idles high) and also reads UART's start bits
when a UART frame begins. There is then another frame which reads the 8 bits of
data as well as the two stop bits and parity bit. This then transitions back to
the "idle" frame.

One thing not addressed here is addressing multiple frequencies. I (jeremy)
expect we could get away with assuming we aren't going to have to read from
different pins each being set at different frequencies, so we might be able to
get away with a simple clock divider.

### TODO:
The first thing is a functional level model. Everyone knows Python so we can
just use that. We should try and write this along with programs to both read
and write UART, SPI, and I2C. That way we know the ISA we are thinking of is
actually reasonable.
