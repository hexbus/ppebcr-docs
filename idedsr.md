# Guide for IDE setup on the PPEB

This sets up the PPEB to access a IDE drive image that defaults to DSK1 emulation.
This guide DOES NOT tell you how to actual use it. That can be found  [Here](hexbus.com/ti99geek/Projects/idedsr/idedsr.html#operation)  from the creator of the IDEDSR.



For this guide you will need to make sure you are running a current version of the PPEB firmware.

 ### Follow the firmware installation guide located [Here](/README.md#firmware-installation)


Also follow the guide for making your SDCard using the rom files found in the latest version of the firmware.  
Be sure to make your autoload.cfg

This is a very important step, as the firmware and roms work together as a single unit.

Next off you need the IDEDSR (Current version as of writing is 1.7)

This is available from Hexbus's IDE Guide [Located Here](https://hexbus.com/ti99geek/Projects/idedsr/idedsr.html#operation)  

[Direct link to the DSR1.7 File](https://hexbus.com/ti99geek/Projects/idedsr/Files/idedsr_v17.zip)

Extract the idedsr zip file somewhere on your PCs harddrive.

Find the ideldr17.bin file where you extracted the file, and rename it to ideldr17-8.bin
Copy that file to your MicroSD Card.

 ### Sanity Check - Test your basic PPEB config.

Next test this current config, by plugging in the PPEB into the TI, and verify the PPEB is working.  

If you have the PICOPEBG.BIN defined as the cart in your autoload.cfg it will be option 2 on the menu.  

If not, you access the PPEB using the CALL PPEB command in basic.

After you have verified that the PPEB is seen on your TI, turn off your TI and retrieve your microsd card.

 ### Preparing your external Storage.

Now you need to format your external USB storage device as ExFat device.

For PC users, I recommend the utility Rufus, located [Here](https://rufus.ie)

![Rufus Options for exFAT](/images/rufus.jpg)

Select your USB drive on the device drop down, boot selection "Non Bootable"  
Volume Label "What Ever you Choose"  
File system "exFAT"  
Make sure Quick format is selected  
Uncheck Create extended label and icon files. 

The press start to reformat your usb drive.

If you have a drive that keeps giving you problems, you can also try removing the quick format option, but this takes a very long time depending on the size and speed of your usb device.


 ### Hard  Drive Images


Next you need a hard drive image.

Here are 2 example ones.

[Harddrive images](files/ExampleIDEImages.zip)

| Name | Description |
|----------|------|
IMAGE1.dat| Contains some files, originaly posted by Rickydean. 
test.cf7|A Blank image with 8 partitions. Maximum size for a IDE image. Posted by JasonACT  

Copy one or both to your USB storage, not the MicroSD Card.

 ### Adding the IDE Option to autoload.cfg

Choose one of the following to add to your autoload.cfg

IDE=1
HARDDISK1=IMAGE1.dat

or 

IDE=1
HARDDISK1=test.cf7


Two drives are supported, but not covered in this document yet.

 ### Installing and Testing the IDEDSR

Plug your USB storage device into your PPEB, 
For the original board, you need a microusb to OTG cable.  

For the PPEB-cr, just plug it into the USB connector on the rear.

Boot your TI, and from the PPEB menu, Load the ideldr17.

From the IDE-DSR V17 Loader do the following.

Select option 1, Loading IDE DSR
It will then promt "Enter IDE-card CRU adderess >1x"  
Press 2 Here.  
It should then show the loading of the dsr, Ending with the line

"Set DSR options... Ok"

Press enter to return to the main menu of the ide-dsr v17 loader,

Select FCTN-9 to exit.

## Testing and saving the IDEDSR


The TI will then reboot.

Select 1 for TI Basic

Check that the IDEDSR is working by using the command 

CALL IDEST

It should give you a pagefull of IDE status.

CALL IDEDIR

This shows the directory of the first partiton of the IDE image.

If these work, you have the DSR loaded correctly, and a valid formatted IDE Image.

To save the IDEDSR to the PPEB so it automatically loads, enter the command

CALL UNMOUNT

and wait until the cursor returns.

You can then power cycle you unit.

To remove IDE dsr from your PPEB, move or delete the IDESRAM.RAM from the root of the microsd card.
Also remember to comment out your IDE and HARDDISK lines from your autoload.cfg


 <br>
  <br>

## [Back to Main Guide](/README.md)