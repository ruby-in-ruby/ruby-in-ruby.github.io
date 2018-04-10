The time of introspection is coming to an end and i am finally producing executables again. (hurrah)

### Block and exception

Even neither ruby blocks or exceptions are implemented i have figured out how to do it, which is sort of good news.
I'll see off course when the day comes, but a plan is made and it is this:

No information lives on the machine stack.

Maybe it's easier to understand this way: All objects live in memory primarily. Whatever get's moved onto the machine
stack is just a copy and, for purposes of the gc, does not need to be considered.

### 4 Objects, 4 registers

As far as i have determined the vm needs internal access to exactly four objects. These are:

- Message: the currently received one, ie the one that in a method led to the method being called
- Self: this is an instance variable of the message
- Frame: local and temporary variables of the method. Also part of the message.
- NewMessage: where the next call is prepared

And, as stated above, all these objects live in memory.

### Single Set Instruction

Self and frame are duplicated information, because then it is easier to transfer. After inital trying, i settle on a
single Instruction to move data around in the vm, Set. It can move instance variables from any of the objects to any
other of the 4 objects.

The implementation of Set ensures that any move to the self slot in Message gets duplicated into the Self register. Same
for the frame, but both are once per method occurances, and both are read only afterwards, so don't need updating later.

Set, like other instructions may use any other variables at any time. Those registers (r4 and up) are scratch.

### Simple call

This makes calling relatively simple and thus easy to understand. To make a call we must be in a method, ie Message,
Self and Frame have been set up.

The method then produces values for the call. This involves operations and the result of that is stored in a variable
(tmp/local/arg). When all values have been calculated a NewMessage is created and all data moved there (see Set)

A Call is then quite simple: because of the duplication of Self and Frame, we only need to push the Message to the
machine stack. Then we move the NewMessage to Message, unroll (copy) the Self into it's register and assign a new
Frame.

Returning is also not overly complicated: Remembering that the return value is an instance variable in the
Message object. So when the method is done, the value is there, not for example in a dedicated register.
So we need to undo the above: move the current Message to NewMessage, pop the previously pushed message from the
machine stack and unroll the Self and Frame copies.

The caller then continues and can pick up the return from it's NewMessage if it is used for further calculation.
It's like it did everything to built the (New)Message and immediately the return value was filled in.

As I said, often we need to calculate the values for the call, so we need to make calls. This happens in exacly the same
way, and the result is shuffled to a Frame slot (local or temporary variable).

### Message creation

Well, i hear, that sounds good and almost too easy. But .... (always one isn't there) what about the Message and Frame
objects, where do you get those from ?

And this is true: in c the Message does not exist, it's just data in registers and the Frame is created on the stack if
needed.

And unfortunately we can't really make a call to get/create these objects as that would create an endless loop. Hmm

We need a very fast way to create and reuse these objects: a bit like a stack. So let's just use a Stack :-)

Off course not the machine stack, but a Stack object. An array to which we append and take from.
It must be global off course, or rather accessible from compiling code. And fast may be that we use assembler, or
if things work out well, we can use the same code as what makes builtin arrays tick.

Still, this is a different problem and the full solution will need a bit time. But clearly it is solvable and does
not impact above register usage convention.

### The fineprint

Just for the sake of completeness: The assumtion i made a the beginning of the Simple Call section, can off course not
possibly be always true.

To boot the vm, we must create the first message by "magic" and place it and the Self (Kernel module reference).
As it can be an empty Message for now, this is not difficult, just one of those little gotachs.