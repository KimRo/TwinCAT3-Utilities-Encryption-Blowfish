# **Data Encryption in TwinCAT PLC with the Blowfish algorithm**


Original text: https://www.embedded.com/encrypting-data-with-the-blowfish-algorithm/

  Modern embedded systems need data security more than ever before. Data security helps keep private data private. Secure data transmissions prevent personal and other data being read by someone other than the intended recipient.

  In cryptographic circles, plaintext is the message you're trying to transmit. That message could be a medical test report, a firmware upgrade, or anything else that can be represented as a stream of bits. The process of encryption converts that plaintext message into ciphertext, and decryption converts the ciphertext back into plaintext. 

  Generally speaking, encryption algorithms come in two flavors, symmetric and public key. Symmetric algorithms, such as Blowfish, use the same key for encryption and decryption. Like a password, you have to keep the key secret from everyone except the sender and receiver of the message. 



## **The Blowfish algorithm**

  Blowfish is a symmetric encryption algorithm, meaning that it uses the same secret key to both encrypt and decrypt messages. Blowfish is also a block cipher, meaning that it divides a message up into fixed length blocks during encryption and decryption. The block length for Blowfish is 64 bits; messages that aren't a multiple of **eight** bytes in size must be padded.

  Blowfish is public domain, and was designed by Bruce Schneier expressly for use in performance-constrained environments such as embedded systems. It has been extensively analyzed and deemed “reasonably secure” by the cryptographic community. Implementation examples are available from several sources, including the one by Bruce Schneier that has been used as a basis for this implementation. https://www.schneier.com/academic/blowfish/download/
  *NOTE*: the bug fix mentioned in the article was included in the TwinCAT code: https://www.schneier.com/wp-content/uploads/2015/12/blowfish-bug-2.txt

  More detailed information on the algorithm can be found here: https://www.schneier.com/academic/blowfish/



## **Use cases**

  - message encryption 
  - file encryption
  - implementing software locks for an application
  - ...



## **TwinCAT**

Beckhoff®, TwinCAT® are registered trademarks of and licensed by Beckhoff Automation GmbH. 
CodeSys® is a registered trademark of and licensed by CODESYS Development GmbH.

  The TwinCAT (The Windows Control and Automation Technology) software system turns almost any PC-based system into a real-time control with multiple PLC, NC, CNC and/or robotics runtime systems. www.beckhoff.com/TwinCAT3
  
  Though TwinCAT is Codesys compliant, the implementation relies on specific libraries of TwinCAT. No attempt has been made nor will be made to make this implementation working on any other CodeSys compliant system.
  
  This implementation is solely tested on and built for TwinCAT 3, no guarantee that the code works on any other Codesys devices.
  Used TwinCAT build: 3.1.4024.22



## **WARNING - Before you start using this code**

Consider the following when using the code:
- In no way this code is official nor advised by Beckhoff Automation GmbH & Co KG.
- A plaintext key is required as input. => It is then either hard coded or open in the system.
- If the same image is used for all machines of a series. => All instances use the same key. => A single compromise affects the whole series.
- The code is based on the Blowfish reference code. The TwinCAT code is a proof of concept and has NOT been thoroughly tested, though some test vectors have been implemented.
- It is released under MIT license, so no warranty, no liability in any form.
- Neither updates nor bugfixes are guaranteed.
- This implementation uses Blowfish as the only encryption mechanism. No ideas for crypto-agility have been implemented.



## **Implementation in TwinCAT**

The implementation uses a subkeyfile with the necessary hex numbers of Pi, necessary to create the 4 S-Boxes and 1 P-Array as per the Blowfish default.

See the example implementation in P_Blowfish
- The subkeyfile BLOWFISH.DAT should be placed in the TwinCAT default boot folder. In most cases this is C:\TwinCAT\3.1\Boot.
- The FB_Blowfish implementation should be initialized by means of a secret key.
	  fbBlowFish(SecretKey:=ADR(key), SecretKeyBytes:=len(key), bLittleEndian := TRUE/FALSE);	
- Be aware that anything that needs to be encrypted should be divisable by *8 bytes* since TwinCAT / CodeSys has no efficient dynamic memory allocation.
    see the sample code  
- The file needs to be correctly read depending on the used hardware.
    - choose bLittleEndian := TRUE for x86 like devices
    - choose bLittleEndian := FALSE for Motorola (ARM) like devices

  
    
## **TODO, but nice to have**
- Implement more test vectors: https://www.schneier.com/wp-content/uploads/2015/12/vectors-2.txt 
- Move the hexadecimal constants of pi (https://www.schneier.com/wp-content/uploads/2015/12/constants-2.txt) to constant P-Array and S-Boxes
