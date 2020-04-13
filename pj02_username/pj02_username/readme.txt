==========================================================
Testing your XVM Translator and submitting files.
==========================================================

Unzip the folder to a convenient location.

Translate each folder and run the CPU Emulator using the provided script.
You are to generate STANDARD Hack Assembly code (that is compliant).

Note that only VMTa has a comparison file. The other programs will
produce an output file that you will submit, but no comparison.

Note that the XVMTe has no test script. The Test.vm file is littered
with errors, so not ASM file should be produced.

The .asm and .out files should be placed (or left) in the folder 
containing the corresponding .vm files (the "program folder").

Any output that your translator produces, such as summaries, tables,
or error messages, should be placed in a text file in the program folder.

It is acceptable to take a screen shot of your output and put the image
file in the program folder.

Your source code should be placed in a folder named "code" at the same 
level as the program folders.

If you want/need a readme.txt, place it at the same level as the program
folders. 

All of these files/folders should be placed the folder pj02_username
Where username is your UCCS username.

Zip up the pj02_username folder into a zip archive named: pj02_username.zip.

Make sure it is a ZIP file, not a RAR, TAR, GAR, ZIP7 or any other type!

When the file is extracted, it should create a folder named pj02_username
and have the folder structure contained within it.

==========================================================
Assumptions: Your XVM Translator
==========================================================

1) can generate STANDARD Hack Assembly Language output.
2) uses the name of the folder as the output name when translating 
   multifile programs.
   
In the "test" folder are several folders. Each folder contains a set of
VM files, including a Sys.vm, a Test.vm. These files are NOT the same 
from folder to folder! Each folder has its own version of these files.

The VMTa folder also includes a Test.tst and a Test.cmp file. This test
exercises the nine ALU commands of the standard VM language and uses most
(if not all) of the other commands and so is a decent test of the 
standard VM Translation capability. The Test.tst and Test.cmp files are
there so that you can run the tests to see if they pass. If you have
passed all of the authors' tests, this one should pass with no problems.

==========================================================
The Test Framework
==========================================================

==========================================================
In the Test.tst file
==========================================================

The test script uses an escapement (a.k.a. tick-tock) mechanism to step
through the tests. This is done using two memory addresses, 2999 and 3000,
and incrementing first one and then the other. The test script runs until
they are not equal (2999 has just been incremented but 3000 hasn't changed)
and then it runs until they are equal again (3000 gets incremented). The 
first loop is the 'tick' and the second is the 'tock'. At the end of the 
tick the current outputs are written to the test file. 

Without the escapement, the first time the loop terminated it would output 
the results and immediately fail the next processing loop and output the 
same results again, straight to the end of the test script. 

This is exactly the reason that clocks have escapements controlled by the
timing device, be it a pendulum or an oscillator. Without them they would
freewheel as fast as they could.

Following the initial set up, the test script has three phases:
Start: Capture the time just before the start of the test function
Run:   Capture the test results
End:   Capture the time just after the end of the test function

The test script needs one copy of the tick/tock/output line for each pass
that it defined in the test.vm file. Similarly, the output definition
may need to be changed to match the test.vm code. But as long as these
are correct the rest of the test script should be unchanged from test
suite to test suite.

==========================================================
In the Sys.vm file:
==========================================================

The program contains two functions: Sys.init() and Sys.output()

In the descriptions below:
TEST=3000: The address of the test counter

Sys.init()
------------
This function stores TEST in static[42] (to emphasize that the indices
are arbitrary) so that both init() and output() have access to it.

This function uses RAM[TEST] and RAM[TEST-1] as the tick-tock counters
plus RAM[TEST] is also the counter. It initializes them to zero (which
they should already be as a result of the test script). When the tick
register is incremented, the test script captures the start time. This
is effectively Test #0.

It then calls the Test.test() function which performs the actual tests.
This takes one argument, which is the location of the test counter.
Once this function returns, it again captures the output which, if
Test.vm is properly synched with the Test.tst, should capture the stop
time and also the total number of test (returned by Test.test()).

The return value is whatever value was returned by Test.test() (although
the code should never get to this point).

Sys.output()
-------------
After each individual test performed by Test.test(), this function will
be called, which does nothing more than increment the tick counter (which
should trigger an output event in the Test.tst file) and then increments
the tock counter (which is also the test counter).

The return value is the new value of the test counter (the pass number).

==========================================================
In the Test.vm file:
==========================================================

This file usually contains a single function, Test.test(), that takes one
argument (the address of the test counter) and uses two local variables
plus one static variable -- primarily to exercise those capabilities.

The first local variable. local[0], holds the value returned by the 
Sys.output() command, while local[1] keeps track of the total number of
tests that have been performed. The number of tests performed in each
pass is stored in static[42]. This is to verify that static indices in 
different files do not collide (since static[42] in the Sys.vm file holds
the address of the TEST array.

On each pass, a block of code performs the test and stores the result in 
success locations in the test array. At the end of the pass the function
calls Sys.output() which should cause the test script to trigger an output
capture of the TEST array to the output file.

After Sys.output() returns, the current test number (that is returned) is
placed in local[0] and the total number of tests is updated in local[1].

Once the last pass of tests if complete, the file does a bogus comparison
on local[0] and local[1] that will always result in the value of local[1]
being returned to Sys.init().
