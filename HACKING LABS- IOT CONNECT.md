**This challenge focuses on exploiting a security flaw related to the broadcast receiver in the "IOT Connect" application, allowing unauthorized users to activate the master switch, which can turn on all connected devices. The goal is to send a broadcast in a way that only authenticated users can trigger the master switch.***

**Step 1** : Installed the app, started it and used drozer to map out the attack surface
https://github.com/aurora-deth/REPORTS/blob/main/1.drozer_mapping.png

**Step 2**:
- I seached for `Master Receiver` on JADX-GUI after decompling the app, and I got the `communication manager` class, which looked intresting
- ![2.commmanger.png](../_resources/2.commmanger.png)
- Looking at the code, it was clear that there was an action been carried out to trigger the master reciver `"MASTER_ON"` this also passed a `key`, I discovered this after much research

**STEP 3**
- I ran this  command on drozer
	- ```python3
	  - run app.broadcast.send --action MASTER_ON
	  ```
	-This returned an error ` "Wrong PIN!!`
**STEP 4**
-  I went ahead and checked futher in the code, and saw the `checker` class
![checker.png](../_resources/checker.png)
- This basically
	- It is decrypted using AES/ECB/PKCS5Padding.
	- The key is an int, converted to a UTF-8 byte array and padded/truncated to 16 bytes.
	- If the decryption result equals "master_on", then check_key() returns true.
- I then prepared a script to crack the password, (from the app, it was a three digit password)
	- ![getting-key.png](../_resources/getting-key.png)
	- the gave me the key as `345`

**STEP 4**

I then ran the adb command with the --ei flag for extra, and passed the key
```
adb shell am broadcast -a MASTER_ON --ei key 345
```
![solved.png](../_resources/solved.png)

## PAWND  PAWND ## PAWND




