# Poll-and-Interrupt-based-device-driver-DD-



<sub><p><strong><span style="font-size: 18px;">Project report</span></strong></p><sub/>
<p>Afonso Pereira 201505870</p>
<p>David Cunha 201604317</p>
<p>Jo&atilde;o Loureiro 201604453</p>
<p><br></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">We have implemented a poll-based <strong>device driver</strong> as well as an interrupt driven one with all the features</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">enumerated up to Lab 4. Furthermore, regarding the interrupt driven Device Driver, we have</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">implemented 7 of the enhancements described in Lab 5: 1) we have tried to minimized the use of global</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">variables; 2) it uses semaphores to eliminate race conditions; 3) it is able to avoid block or busy waiting</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">using the O_NONBLOCK flag; 4) ioctl operations were added; 5) it allows the user to interrupt a</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">process inside a read syscall; 6) access control was implemented in order to prevent more than one</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">&ldquo;user&rdquo; to access the serial port; 7)it supports the select/poll system call.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">It is worth mentioning that, although the seri implementation allows access to 4 device drivers, only</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">seri0 has updates regarding received and transmitted information, being the only one operational.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);"><br></span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);"><strong></strong></span><strong><span style="font-size: 19px;;background-color: rgb(255, 255, 255);">Enhancements:</span></strong><span style="background-color: rgb(255, 255, 255);"><strong></strong></span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">3.1) The usage of global variables consists of 2 integers that hold the number of drivers possible and</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">the Major number, and a pointer of type struct seri_dev which is the data structure each device has.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">This variable is then allocated in memory containing an array of 4 elements. This data structure holds</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">several types need for the implementation of each device. Besides these variables, there is also a</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">variable of type struct file_operations used to indicate which functions represent each operation. (seri.c,</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">lines 41-568)</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">3.2) We eliminated race conditions using semaphores for the read and write system call. This is</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">implemented using the variable struct semaphore mutex, which is a variable present in the struct</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">seri_dev of each device. This variable is initialized with 1. (seri.c, line 249 and 309).</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">3.3) On the condition statement beginning in line 255 of the file seri.c we honour the O_NONBLOCK</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">flag. This flag is only important in the read system call. Basically the if statement checks for the flag,</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">and if so just returns from the function in order not to block. The file to test this is test/ononblockTest.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">3.4) We created the function seri_ioctl, lines 155-218 of the file seri.c, which implements ioctl</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">operations. The implemented operations are as follows: to get and set the bitrate, the number of</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">information bits, parity and number of stop bits. In order to execute these operations, the message</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">received and returned by the ioctl is composed of 24 bits with the format: DLM DLL LCR. This</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">message is the decoded by either the user or device driver. The file to test this is test/ioctlTest. In this</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">test the users is expected to call the function with the arguments: GET or SET BITRATE</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">CHAR_WIDTH STOP_BITS PARITY.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">3.5) In lines 262-268 of the file seri.c, the possibility for user to terminate reading is implemented.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">This implementation makes use of the function wait_event_interruptible (&hellip;) which returns the error -</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">ERESTARTSYS if there was an interruption. This is then checked, and the system call returns if it was</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">indeed the return of the function. The file to test this is test/readTest where the user simply needs to</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">execute Ctrl-C.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">3.6) By implementing the condition statement in line 129 of the file seri.c, we prevent more than one</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">user to open the device. The variable n_users which is assigned a 0 at initialization, is checked to see if&nbsp;</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">it&rsquo;s 1 or 0. 1 means that there is a user already using the device, otherwise the device is free to use. The</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">file to test this is test/nusersTest which tries to open the same device twice.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">3.8) Select/poll operations were added in function seri_poll (lines 220-238 of the file seri.c). this</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">function simply checks if the size of the transmitter FIFO is full and if the size of the receiver FIFO is</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">empty. It then returns a mask containing with information on whether the device is readable or writable.</span></p>
<p style="text-align: left;"><span style="background-color: rgb(255, 255, 255);">The file to test this is test/selectTest.</span></p>
