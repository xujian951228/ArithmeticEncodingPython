# ArithmeticEncodingPython

This project implements the lossless data compression technique called **arithmetic encoding (AE)**. The project is simple and has just some basic features.

The project has a main module called `pyae.py` which contains a class called `ArithmeticEncoding` to encode and decode messages.

# Usage Steps

To use the project, follow these steps:

1. Import `pyae`
2. Instantiate the `ArithmeticEncoding` Class
3. Prepare a Message
4. Encode the Message
5. Decode the Message

## Import `pyae`

The first step is to import the `pyae` module.

```python
import pyae
```

## Instantiate the `ArithmeticEncoding` Class

Create an instance of the `ArithmeticEncoding` class. Its constructor accepts the frequency table which is a dictionary mapping each possible character to its frequency. According to this frequency table, the messages to be encoded/decoded must have only the 3 characters **a**, **b**, and **c**.

```python
frequency_table = {"a": 2,
                   "b": 7,
                   "c": 1}

AE = pyae.ArithmeticEncoding(frequency_table)
```

## Prepare a Message

Prepare the message to be compressed. All the characters in this message must exist in the frequency table.

```python
original_msg = "abc"
```

## Encode the Message

Encode the message using the `encode()` method. It accepts the message to be encoded and the probability table. It returns the encoder and the encoded message (single double value).

```python
encoder, encoded_msg = AE.encode(msg=original_msg, 
                                 probability_table=AE.probability_table)
```

## Decode the Message

Decode the message using the `decode()` method. It accepts the encoded message, message length, and the probability table. It returns the decoder and the decoded message.

```python
decoder, decoded_msg = AE.decode(encoded_msg=encoded_msg, 
                                 msg_length=len(original_msg),
                                 probability_table=AE.probability_table)
```

# <u>IMPORTANT</u>: `double` Module

The floating-point numbers in Python are limited to a certain precision. Beyond it, Python cannot store any additional decimal numbers. This is why the project uses the double data type offered by the [`decimal` module](https://docs.python.org/2/library/decimal.html).

The `decimal` module has a class named `Decimal` that can use any precision. The precision can be changed using the `prec` attribute as follows:

```python
getcontext().prec = 50
```

The precision defaults to 28. It is up to the user to set the precision to any value that serves the application. Note that the precision only affects the arithmetic operations. 

For more information about the `decimal` module, check its [documentation](https://docs.python.org/2/library/decimal.html): https://docs.python.org/2/library/decimal.html

# Example

The [`example.py`](/example.py) script has an example that compresses the message `abc` using arithmetic encoding. The precision of the `decimal` data type is left to the default value 28 as it can encode the message `abc` without losing any information. 

```python
import pyae

# Example for encoding a simple text message using the PyAE module.

frequency_table = {"a": 2,
                   "b": 7,
                   "c": 1}

AE = pyae.ArithmeticEncoding(frequency_table)

original_msg = "abc"
print("Original Message: {msg}".format(msg=original_msg))

encoder, encoded_msg = AE.encode(msg=original_msg, 
                                 probability_table=AE.probability_table)
print("Encoded Message: {msg}".format(msg=encoded_msg))

decoder, decoded_msg = AE.decode(encoded_msg=encoded_msg, 
                                 msg_length=len(original_msg),
                                 probability_table=AE.probability_table)
print("Decoded Message: {msg}".format(msg=decoded_msg))

print("Message Decoded Successfully? {result}".format(result=original_msg == decoded_msg))
```

The printed messages out of the code are:

```
Original Message: abc
Encoded Message: 0.1729999999999999989175325511
Decoded Message: abc
Message Decoded Successfully? True
```

So, the message `abc` is encoded using the double number `0.173`.

It is possible to print the encoder to get information about the stages of the encoding process. The encoder is a list of dictionaries where each dictionary represents a stage.

```python
print(encoder)
```

```python
[{'a': [Decimal('0'), Decimal('0.6999999999999999555910790150')],
  'b': [Decimal('0.6999999999999999555910790150'),
   Decimal('0.7999999999999999611421941381')],
  'c': [Decimal('0.7999999999999999611421941381'),
   Decimal('0.9999999999999999722444243844')]},
 {'a': [Decimal('0'), Decimal('0.4899999999999999378275106210')],
  'b': [Decimal('0.4899999999999999378275106210'),
   Decimal('0.5599999999999999372723991087')],
  'c': [Decimal('0.5599999999999999372723991087'),
   Decimal('0.6999999999999999361621760841')]},
 {'a': [Decimal('0.4899999999999999378275106210'),
   Decimal('0.5389999999999999343303080934')],
  'b': [Decimal('0.5389999999999999343303080934'),
   Decimal('0.5459999999999999346633750008')],
  'c': [Decimal('0.5459999999999999346633750008'),
   Decimal('0.5599999999999999353295088156')]},
 {'a': [Decimal('0.5459999999999999346633750008'),
   Decimal('0.5557999999999999345079437774')],
  'b': [Decimal('0.5557999999999999345079437774'),
   Decimal('0.5571999999999999346522727706')],
  'c': [Decimal('0.5571999999999999346522727706'),
   Decimal('0.5599999999999999349409307570')]}]
```

## Low Precision

Assume the message to be encoded is `"abc"*20` (i.e. `abc` repeated 20 times) while using the default precision 28. The length of the message is 60.

```python
original_msg = "abc"*20
```

Here is the code that uses this new message.

```python
import pyae

frequency_table = {"a": 2,
                   "b": 7,
                   "c": 1}

AE = pyae.ArithmeticEncoding(frequency_table)

original_msg = "abc"*20
print("Original Message: {msg}".format(msg=original_msg))

encoder, encoded_msg = AE.encode(msg=original_msg, 
                                 probability_table=AE.probability_table)
print("Encoded Message: {msg}".format(msg=encoded_msg))

decoder, decoded_msg = AE.decode(encoded_msg=encoded_msg, 
                                 msg_length=len(original_msg),
                                 probability_table=AE.probability_table)
print("Decoded Message: {msg}".format(msg=decoded_msg))

print("Message Decoded Successfully? {result}".format(result=original_msg == decoded_msg))
```

By running the previous code, here are the results of the print statements. The decoded message is different from the original message. The reason is that the current precision of 28 is not sufficient to encode a message of length 60.

```
Original Message: abcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabc
Encoded Message: 0.1683569979716024329522342419
Decoded Message: abcabcabcabcabcabcabcabcabcabcabcabcabcabcabbcbbbbbbbbbbbbbb
Message Decoded Successfully? False
```

In this case, the precision should be increased. Here is how to change the precision to be 45:

```python
from decimal import getcontext

getcontext().prec = 45
```

Here is the new code after increasing the precision of the `Double` data type:

```python
import pyae
from decimal import getcontext

getcontext().prec = 45

frequency_table = {"a": 2,
                   "b": 7,
                   "c": 1}

AE = pyae.ArithmeticEncoding(frequency_table)

original_msg = "abc"*20
print("Original Message: {msg}".format(msg=original_msg))

encoder, encoded_msg = AE.encode(msg=original_msg, 
                                 probability_table=AE.probability_table)
print("Encoded Message: {msg}".format(msg=encoded_msg))

decoder, decoded_msg = AE.decode(encoded_msg=encoded_msg, 
                                 msg_length=len(original_msg),
                                 probability_table=AE.probability_table)
print("Decoded Message: {msg}".format(msg=decoded_msg))

print("Message Decoded Successfully? {result}".format(result=original_msg == decoded_msg))
```

After running the code, here are the results where the original message is restored successfully:

```
Original Message: abcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabc
Encoded Message: 0.168356997971602432952234241597600194030293262
Decoded Message: abcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabcabc
Message Decoded Successfully? True
```

# Contact Us

- E-mail: [ahmed.f.gad@gmail.com](mailto:ahmed.f.gad@gmail.com)
- [LinkedIn](https://www.linkedin.com/in/ahmedfgad)
- [Amazon Author Page](https://amazon.com/author/ahmedgad)
- [Heartbeat](https://heartbeat.fritz.ai/@ahmedfgad)
- [Paperspace](https://blog.paperspace.com/author/ahmed)
- [KDnuggets](https://kdnuggets.com/author/ahmed-gad)
- [TowardsDataScience](https://towardsdatascience.com/@ahmedfgad)
- [GitHub](https://github.com/ahmedfgad)





