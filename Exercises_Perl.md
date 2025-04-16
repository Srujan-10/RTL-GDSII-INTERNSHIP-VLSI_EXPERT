# Exercise Questions 
1. Write a Perl program that takes a user's name as input and prints a greeting message.
2. Write a Perl program to calculate the factorial of a number.
3. Write a Perl program to generate the Fibonacci sequence up to a given number.
4. Write a Perl program to sort an array of numbers in ascending order.
5. Write a Perl program to merge two arrays and remove duplicate elements.
6. Write a Perl program to find the largest and smallest elements in an array.
7. Write a Perl program that defines a subroutine to check if a number is prime.
8. Write a Perl program that takes an array of numbers as input and uses a subroutine to find the average.

## 1. Write a Perl program that takes a user's name as input and prints a greeting message.
code :

```
my $name = <STDIN>;
chomp($name);
print("WELCOME $name !");

```
output : 

```
Arjun
WELCOME Arjun !
```
## 2. Write a Perl program to calculate the factorial of a number.
code: 
```
my $n = <STDIN>;
chomp($n);
my $fact=1;
for($i=1;$i<=$n;$i++){
$fact *=$i;
}
print "the factorial of the number $n is $fact";
```
output :
```
20
the factorial of the number 20 is 2432902008176640000
```
