---
title: 逆波兰表达式
date: 2018-04-14 12:52:27
tags: [算法]
categories: 算法
top: 100
---
一个字符串表达式如：`1+2*3-4+2*(5-2*(2-1))`,求出表达式值，答案为9。
这种类型的题目可用逆波兰表达式解决，逆波兰表达式又称作后缀表达式，在四则混合运算中经常用到。
`4+5*(3-2)`逆波兰式是`4532-*+`
`1+2*3/3/4*2-4+2*(5-2*(2-1))`逆波兰式是`123*3/4/2*4-25221-*-*++`
`1+2*3-4+2*(5-2*(2-1))`逆波兰式是`123*4-25221-*-*++`
算法如下:
<!--more-->
``` java
public class ReversePolishNotation {

	public static void main(String[] args) {

		String expression = "1+2*3/3/4*2-4+2*(5-2*(2-1))";
		// String expression = "1+2*3-4+2*(5-2*(2-1))";
		List<Character> a = createRPN(expression);
		System.out.println(a);
		int b = calc(a);
		System.out.println(b);
	}

	// 生成逆波兰表达式
	public static List<Character> createRPN(String expression) {
		if (null == expression || "".equals(expression)) {
			return null;
		}

		Stack<Character> operandStack = new Stack<Character>();
		Stack<Character> operatorStack = new Stack<Character>();

		char[] elements = expression.toCharArray();

		for (int i = 0; i < elements.length; i++) {
			char element = elements[i];
			if (Character.isDigit(element)) {
				operandStack.push(element);
			} else if (element == '(') {
				operatorStack.push(element);
			} else if (element == ')') {
				char s;
				while ((s = operatorStack.pop()) != '(') {
					operandStack.push(s);
				}
			} else {
				if (operatorStack.empty()) {
					operatorStack.push(element);
				} else if (priorty(element, operatorStack.peek())) {
					operatorStack.push(element);
				} else {
					operandStack.push(operatorStack.pop());
					operatorStack.push(element);
				}
			}
		}

		while (!operatorStack.empty()) {
			operandStack.push(operatorStack.pop());
		}
		List<Character> s = new ArrayList<Character>();
		while (!operandStack.empty()) {
			s.add(operandStack.pop());
		}

		return s;
	}

	// 比较字符优先级
	//当新添加的操作费是 * /，或者栈顶是(时，操作符优先级高
	public static boolean priorty(char s1, char s2) {
		if ((s1 == '*' || s1 == '/') && (s2 == '+' || s2 == '-')) {
			return true;
		}
		if ((s2 == '(' || s2 == ')')) {
			return true;
		} else {
			return false;
		}
	}

	// 波兰表达式计算
	public static int calc(List<Character> expression) {
		Stack<Integer> calcStack = new Stack<Integer>();
		int sum = 0;
		int num1;
		int num2;
		for (int i = expression.size() - 1; i >= 0; i--) {
			char c = (char) expression.get(i);
			if (Character.isDigit(c)) {
				calcStack.push(c - '0');
			}
			if (c == '+') {
				num1 = calcStack.pop();
				num2 = calcStack.pop();
				calcStack.push(num2 + num1);
			} else if (c == '-') {
				num1 = calcStack.pop();
				num2 = calcStack.pop();
				calcStack.push(num2 - num1);
			} else if (c == '*') {
				num1 = calcStack.pop();
				num2 = calcStack.pop();
				calcStack.push(num2 * num1);
			} else if (c == '/') {
				num1 = calcStack.pop();
				num2 = calcStack.pop();
				calcStack.push(num2 / num1);
			}
		}

		return calcStack.pop();
	}
}

```