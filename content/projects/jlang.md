---
title: JLang Compiler
period: "2026"
---

Compiler for a simple C-like programming language in C using LLVM codegen backend. It might be more accurate to describe it as an LLVM frontend with ad-hoc lexing and recursive descent parser. Source [here](https://github.com/glolichen/jlang).

JLang supports: for loops, if and if/else, integer types (8/16/32/64 bit signed), strict and static type checking, `getchar`/`putchar` terminal IO, functions and standard signed integer arithmetic. 

I tried to avoid using the stack (and therefore the LLVM `mem2reg` pass), so the JLang compiler generates the static-single assignment form LLVM-IR directly (and therefore I had to program all control flows manually). This was a serious mistake in hindsight as things like [loops](https://github.com/glolichen/jlang/blob/main/src/codegen/forloop.c) have fairly horrific control flows when accounting for all possibilities. Pointers (and therefore arrays) are also quite complicated to deal with in SSA so I didn't implement them. As a result JLang is *not* Turing complete because it cannot access unbounded storage (memory). But learning how to generate/debug CFGs (control flow graphs, not context free grammar) manually and SSA form (phi nodes, etc) was cool.

IO is possible but highly impractical. For example, [here](https://github.com/glolichen/jlang/blob/main/test/fibonacci.jlang) is a Fibonacci program:
```
i32 main() {
	i32 n;
	i32 input;
	i32 inputdigits;
	i32 i;
	i32 answer;
	i32 prev;
	i32 prevprev;
	i32 current;
	i32 next;
	i32 answerdigits;
	i32 maxpower;
	i32 power;
	i32 digit;

	n = toi32(0);
	input = toi32(48);
	inputdigits = -toi32(1);

	for (i = toi32(1000000000); input != toi32(10); i = i / toi32(10)) {
		n = n + i * (input - toi32(48));
		inputdigits = inputdigits + toi32(1);
		input = toi32(getchar());
	}

	for (i = toi32(0); i < toi32(10) - inputdigits - toi32(1); i = i + toi32(1)) {
		n = n / toi32(10);
	}

	answer = toi32(1);
	if (n > toi32(2)) {
		prev = toi32(1);
		prevprev = toi32(1);
		current = toi32(2);
		for (i = toi32(0); i < n - toi32(3); i = i + toi32(1)) {
			next = prev + current;
			prevprev = prev;
			prev = current;
			current = next;
		}
		answer = current;
	}
	else {
		if (n <= toi32(0)) {
			answer = toi32(0);
		}
	}

	if (answer == toi32(0)) {
		putchar(toi32(48));
	}
	else {
		answerdigits = toi32(0);
		maxpower = toi32(1);
		for (; maxpower <= answer; maxpower = maxpower * toi32(10)) { }

		for (power = maxpower / toi32(10); power >= toi32(1); power = power / toi32(10)) {
			digit = answer / power;
			putchar(digit + toi32(48));
			answer = answer % power;
		}
	}

	putchar(toi32(10));

	return toi32(0);
}
```

Uhh, [it just works](https://www.youtube.com/watch?v=nVqcxarP9J4).

Anyway, learning about SSA, types, writing a lexer and parser, writing out a context free grammar for the language, etc was cool. I read the LLVM [Kaleidoscope](https://llvm.org/docs/tutorial/) example (and a [C implementation](https://github.com/benbjohnson/llvm-c-kaleidoscope)), and parts of Crafting Interpreters (Nystrom) and Engineering a Compiler (Cooper and Torczon). Using LLVM as a backend instead of implementing my own codegen/interpreter/virtual machine is really cheating but I didn't want to spend too much time on this. I'm excited to take more serious compiler classes in college in the future (and plan to in Spring 2027).
