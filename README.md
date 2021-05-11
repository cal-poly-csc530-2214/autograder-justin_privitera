# Automated Feedback Generation for Introductory Programming Assignments

After much difficulty trying to build Sketch, I decided to go a different route entirely. Rather than coding something, I opted to read some of the lecture notes you posted on piazza which happen to be very relevant to this week's paper (https://people.csail.mit.edu/asolar/SynthesisCourse/index.htm). I hope not coding anything won't get me in trouble! Here is a summary and my thoughts:

## Summary of Lecture 7 and 8

Surprise surprise! The course these notes are from is taught by the same guy who wrote the Sketch programmer's manual. Something seems fishy here...

These two lectures were an interesting dive into Sketch, how it works, and what it can do. I think this was an excellent complement to the paper we read for this week, and would have been good preliminary material, had I known about it before reading our paper. To summarize, the idea behind constraint-based synthesis is to parametrize a program P as P(c) such that requirements on the behavior of P(c) are transformed into requirements on the parameters c such that any value of c satisfying the constraints will lead to a program P(c) satisfying the requirements. 

Sketch is a tool that allows users to write parametric programs, as opposed to describing a space of programs and letting a compiler do the work. It does so with 3 main components: holes, test harnesses, and generators. I think the most interesting of the three is generators, which are extremely powerful. Generators are similar to functions except they will get fully inlined and partially evaluated. Generators can be filled with holes which allow users to describe a space of functions. Additionally, they can be recursive, and the holes at each level can be filled with different values. Finally, they are high-order because they can take other functions or generators as parameters.

Because they are recursive, the number of holes is unbounded, so the user must specify a recursion limit at compile time. I believe the same thing is done with loops, so loops are unrolled k times, bounding the number of holes. The possibilities for filling in the holes are thus always finite (?). In other words, sketch guarantees there is a finite number of maps from hole names and calling contexts to values.

Moving on to the next lecture, which was a bit harder to understand, we get in to denotational semantics. I think we decided on the first day of class (?) that denotational semantics was not as cool as operational semantics. Near the end, various optimizations were discussed, including structural hashing and algebraic simplification. Other aspects of this lecture have been worked in to the preceding paragraphs.

## My Thoughts

I thought generators were very very cool. A very powerful example is shown in which sketch is used to generate a program that will reverse the bits in a 32 bit word using shifts and masks. The code with holes and generators is very pretty, and the generated code is rather ugly, especially with regards to the many underscores on all the variable names, yet super cool to see how the power of sketch was harnessed to make something meaningful. If you just glance at the generated code, it isn't clear what it's doing, yet it is also quite efficient. This example makes use of the function inlining, as the recursive generator just becomes a sequence of instructions. All in all, very neat to see how Sketch was able to take what seems almost like a specificiation and turn it into very efficient, albeit ugly, code.

_The code for the generator function used in the next function_

	generator void rep(int n, fun f)
	{
		if(n>0)
		{
			f();
			rep(n - 1, f);
		}
	}

_The example in question_

	bit[32] reverseSketch(bit[32] in)
	{
		bit[32]  t = in;
		int s = 1;
		generator void tmp()
		{
			bit[32] m = ??;
			t = ((t << s) & m )| ((t >> s) & (~m));
			s = s*??;
		}
		rep(??, tmp);
		return t;
	}

_The generated code_

	void reverseSketch (bit[32] in, ref bit[32] _out)  implements reverse/*reverse.sk:7*/
	{
		bit[32] __sa0 = {0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1,0,1};
		_out = ((in << 1) & __sa0) | ((in >> 1) & (~(__sa0)));
		bit[32] __sa0_0 = {0,0,1,1,0,0,1,1,0,0,1,1,0,0,1,1,0,0,1,1,0,0,1,1,0,0,1,1,0,0,1,1};
		_out = ((_out << 2) & __sa0_0) | ((_out >> 2) & (~(__sa0_0)));
		bit[32] __sa0_1 = {0,0,0,0,1,1,1,1,0,0,0,0,1,1,1,1,0,0,0,0,1,1,1,1,0,0,0,0,1,1,1,1};
		_out = ((_out << 4) & __sa0_1) | ((_out >> 4) & (~(__sa0_1)));
		bit[32] __sa0_2 = {0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1};
		_out = ((_out << 8) & __sa0_2) | ((_out >> 8) & (~(__sa0_2)));
		bit[32] __sa0_3 = {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1};
		_out = ((_out << 16) & __sa0_3) | ((_out >> 16) & (~(__sa0_3)));
		return;
	}

Holes seem to be the unsung heroes of many subfields of PL.

Perhaps I'm wrong, but I feel like I may have gained a greater understanding of Sketch from reading this general overview of it than I would have gotten from playing around with it. I ought to write more but I'm also out of time so I will end my book report here. Happy grading :)
