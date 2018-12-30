# ultra secret

## Challenge

This flag is protected by a password stored in a highly sohpisticated chain of hashes. Can you capture it nevertheless? We are certain the password consists of lowercase alphanumerical characters only.

nc 35.207.158.95 1337

[Source](https://35c3ctf.ccc.ac/uploads/juniorctf/ffb8d1ab6ff961419bee1cf1cddfb2e5-ultra_secret.tar)

Difficulti estimate: Easy-Medium

## Solution

The source code is given, so let's have a look at it:

```rust
extern crate crypto;

use std::io;
use std::io::BufRead;
use std::process::exit;
use std::io::BufReader;
use std::io::Read;
use std::fs::File;
use std::path::Path;
use std::env;

use crypto::digest::Digest;
use crypto::sha2::Sha256;

fn main() {
    let mut password = String::new();
    let mut flag = String::new();
    let mut i = 0;
    let stdin = io::stdin();
    let hashes: Vec<String> = BufReader::new(File::open(Path::new("hashes.txt")).unwrap()).lines().map(|x| x.unwrap()).collect();
    BufReader::new(File::open(Path::new("flag.txt")).unwrap()).read_to_string(&mut flag).unwrap();

    println!("Please enter the very secret password:");
    stdin.lock().read_line(&mut password).unwrap();
    let password = &password[0..32];
    for c in password.chars() {
        let hash =  hash(c);
        if hash != hashes[i] {
            exit(1);
        }
        i += 1;
    }
    println!("{}", &flag)
}

fn hash(c: char) -> String {
    let mut hash = String::new();
    hash.push(c);
    for _ in 0..9999 {
        let mut sha = Sha256::new();
        sha.input_str(&hash);
        hash = sha.result_str();
    }
    hash
}
```

From studying the source code we can learn that every character from the password we provide
will be chain hashed 10'000 times and then compared to the corresponding hash in the `hashes.txt` file.
So, the first character of the password will be hashed and compared to the first hash in `hashes.txt`.
The second character of the password will be hashed and compared to the second hash in `hashes.txt`.
And so forth ...

A soon as the first hash doesn't match the program terminates.
If all the hashes matched, the flag is revealed.

The problem with the above program is that chain hashing 10'000 times takes a measurable amount of time.
Thus, we can launch a timing attack.
The longer a password takes to verify the more characters from the beginning of the password are correct.
With that in mind and a few Python lines later we can exploit the timing of the hash algorithm and
get to the hash.

```python
import time
import socket

POSSIBLE_CHARS = "abcdefghijklmnopqrstuvwxyz0123456789"
HOST = "35.207.158.95"
PORT = 1337
PASS_LEN = 32

guessed_chars = []

for i in range(PASS_LEN):
    best_char = ("a", float("-inf"))
    for c in POSSIBLE_CHARS:
        # construct the new password guess
        guess = (
                "".join(guessed_chars) + c * (PASS_LEN - len(guessed_chars))
        ).encode("ascii")

        # connect to the exploitable service and provide the password
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((HOST, PORT))
        start = time.time_ns()
        s.recv(4096)  # read but ignore the prompt
        s.send(guess + b"\n")
        flag = s.recv(4096)
        if flag:
            print("Profit from the flag: '{}'".format(flag))
            exit(0)

        end = time.time_ns()
        duration = end - start
        if duration > best_char[1]:
            best_char = (c, duration)
        s.close()

    guessed_chars.append(best_char[0])
    print("Best character for {} is {} leading to {}".format(
        i, best_char[0], "".join(guessed_chars)))

print("Guessed password is:", "".join(guessed_chars), guessed_chars)
```
