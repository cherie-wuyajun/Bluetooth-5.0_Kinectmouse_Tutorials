# Bluetooth 5.0_Kinectmouse_Tutorials

## Part 1 preparation

We use the WICED  CYW920719Q40EVB-01, it is a Ultra-Low-Power Bluetooth 5.0 board, and our system is  Windows 10. 



Our task is to make a kinectmouse using this board. First step, make sure you successfully installed the WICED-Studio in the version greater than 6.2 as well as the examples in the WICED website. You will find both of them in the following link.

https://community.cypress.com/docs/DOC-15206

Now plug in your board to check if the yellow light is on, which showed that the board is on charged. Then open your device manager to check the serial ports. If you would find two new ports in your screen, one is WICED HCI UART, the other is WICED Peripheral UART, which means that your board is connected to your computer successfully. 



Now, we will try the given example. Open your WICED-Studio, your would find a folder named 20719-B1_Bluetooth, then you open apps->demo->hid->hci_ble_hid_dev. Our project would based on this given example, as the kinectmouse is still a hid device.

HCI: Host Controller Interface

BLE: Bluetooth Low Energy

HID: Human Interface Device





## Part 2  Raw Data Control Mouse

First, we copy the hci_ble_hid_dev folder to back up, as we will modify the folder directly.  Then open the following link, we would find the HID Descriptor Tool in this website. If you want to develop your own hid device, you would also use this tool to generate the hid description quickly. When you open HID Descriptor Tool, you will see the screen below. Then enter the mouse, as today we are make a kinectmouse. The Report Descriptor showed on the screen is the what we should put into our program.

https://www.usb.org/document-library/hid-descriptor-tool



Then, we modify the hci_ble_hid_dev_db.c. The following code is where we need change. The original version is about keyboard, we change it into the mouse one.

    uint8_t hci_ble_hid_dev_generic_access_device_name[]                                      = {'h','c','i','_','b','l','e','_','h','i','d','_','d','e','v','i','c','e'};
    uint8_t hci_ble_hid_dev_generic_access_appearance[]                                       = {BIT16_TO_8(APPEARANCE_GENERIC_HID_DEVICE)};
    uint8_t hci_ble_hid_dev_human_interface_device_report_map[]                               = {
        // STD_KB_REPORT_ID
        // Input Report, 8 bytes
        // 1st byte:Keyboard LeftControl/Keyboard Right GUI
        // 2nd byte:Constant, 3rd ~ 6th: keycode
        // Output Report, 1 byte: LED control
        0x05 , 0x01,                    // USAGE_PAGE (Generic Desktop)
        0x09 , 0x06,                    // USAGE (Keyboard)
        0xA1 , 0x01,                    // COLLECTION (Application)
        0x85 , 0x01,                    //    REPORT_ID (1)
        0x75 , 0x01,                    //    REPORT_SIZE (1)
        0x95 , 0x08,                    //    REPORT_COUNT (8)
        0x05 , 0x07,                    //    USAGE_PAGE (Keyboard)
        0x19 , 0xE0,                    //    USAGE_MINIMUM (Keyboard LeftControl)
        0x29 , 0xE7,                    //    USAGE_MAXIMUM (Keyboard Right GUI)
        0x15 , 0x00,                    //    LOGICAL_MINIMUM (0)
        0x25 , 0x01,                    //    LOGICAL_MAXIMUM (1)
        0x81 , 0x02,                    //    INPUT (Data,Var,Abs)
        0x95 , 0x01,                    //    REPORT_COUNT (1)
        0x75 , 0x08,                    //    REPORT_SIZE (8)
        0x81 , 0x03,                    //    INPUT (Cnst,Var,Abs)
        0x95 , 0x05,                    //    REPORT_COUNT (5)
        0x75 , 0x01,                    //    REPORT_SIZE (1)
        0x05 , 0x08,                    //    USAGE_PAGE (LEDs)
        0x19 , 0x01,                    //    USAGE_MINIMUM (Num Lock)
        0x29 , 0x05,                    //    USAGE_MAXIMUM (Kana)
        0x91 , 0x02,                    //    OUTPUT (Data,Var,Abs)
        0x95 , 0x01,                    //    REPORT_COUNT (1)
        0x75 , 0x03,                    //    REPORT_SIZE (3)
        0x91 , 0x03,                    //    OUTPUT (Cnst,Var,Abs)
        0x95 , 0x06,                    //    REPORT_COUNT (6)
        0x75 , 0x08,                    //    REPORT_SIZE (8)
        0x15 , 0x00,                    //    LOGICAL_MINIMUM (0)
        0x26 , 0xFF , 0x00,             //    LOGICAL_MAXIMUM (255)
        0x05 , 0x07,                    //    USAGE_PAGE (Keyboard)
        0x19 , 0x00,                    //    USAGE_MINIMUM (Reserved (no event indicated))
        0x29 , 0xFF,                    //    USAGE_MAXIMUM (Reserved (no event indicated))
        0x81 , 0x00,                    //    INPUT (Data,Ary,Abs)
        0xC0,                           // END_COLLECTION
        0x05, 0x0C,                     // USAGE_PAGE (Consumer Devices)
        0x09, 0x01,                     // USAGE (Consumer Control)
        0xA1, 0x01,                     // COLLECTION (Application)
        0x85, 0x02,                     //      Report ID (2)
        0x75, 0x10,                     //      REPORT_SIZE (10)
        0x95, 0x02,                     //      REPORT_COUNT (2)
        0x15, 0x01,                     //      LOGICAL_MINIMUM (1)
        0x26, 0x8c, 0x02,               //      LOGICAL_MAXIMUM (28c)
        0x19, 0x01,                     //      USAGE_MINIMUM (Button 1)
        0x2a, 0x8c, 0x02,               //      USAGE_MAXIMUM (Button 28c)
        0x81, 0x60,                     //      INPUT (data, array, abs)
        0xc0,                           // END COLLECTION
    };

    uint8_t hci_ble_hid_dev_generic_access_appearance[]                                       = {BIT16_TO_8(APPEARANCE_HID_MOUSE)};
    uint8_t hci_ble_hid_dev_human_interface_device_report_map[]                               = {
        0x05, 0x01, // usage page
        0x09, 0x02, // usage (mouse)
        0xa1, 0x01, // Application
            //0x85, 0x01, // report id (1)
            0x09, 0x01, // usage (pointer)
            0xa1, 0x00, // collection (physical)
                0x05, 0x09, // usage page (button)
                0x19, 0x01, // usage_minimum (button 1)
                0x29, 0x03, // usage_maximum (button 3)
                0x15, 0x00, // logical_minimum
                0x25, 0x01, // logical_maximum
                0x95, 0x03, // report_count
                0x75, 0x01, // report size 1*3 --buttons--
                0x81, 0x02, // input (data, var, abs) button state
                0x95, 0x01, // report count
                0x75, 0x05, // report size 1*5 bit --filler--
                0x81, 0x03, // input (cnst, var, abs) axis state
                0x05, 0x01, // usage_page (1)
                0x09, 0x30, // usage (x axis)
                0x09, 0x31, // usage (y axis)
                0x15, 0x81, // logical minimum (-127)
                0x25, 0x7f, // logical maximum (127)
                0x75, 0x08, // report size (8)
                0x95, 0x02, // report count (2) 2*bit --axis--
                0x81, 0x06, // input (data, var, rel)
            0xc0, // end collection
        0xc0 // end collection
    };

The next step is modify the hci_ble_hid_dev.c, The first place is in the initial part, the defination: 

    #define HCI_BLE_HID_DEV_INPUT_REPORT_SIZE   8
    //different

Then we do some change to the hci_ble_hid_dev_set_advertisement_data function. All the change is listed below. You would find that #ifdef HID_MOUSE, the following branch is what we added, #else branch is the original code for general device.

    static void hci_ble_hid_dev_set_advertisement_data( void )
    {
    #ifdef HID_MOUSE
        uint16_t appearence = APPEARANCE_HID_MOUSE;
    #else
        uint16_t appearence = APPEARANCE_GENERIC_HID_DEVICE;
    #endif
    //When start advertising, the info would tell others which device it is.
    }



## Part 3 Make target & Download program

After you successfully modify your code. You would make target. You click make target and chose new. Then you enter the name of our target: demo.hid.hci_ble_hid_dev-CYW920719Q40EVB_01 download. After that, you could see the target add on the list. Double click, you would see the log in the Console.

If everything goes well, you will see the following output in Console.

    23:40:50 **** Build of configuration Release for project 20719-B1_Bluetooth ****
    "C:\\Users\\Yajun Wu\\Documents\\WICED-Studio-6.2\\20719-B1_Bluetooth\\make.exe" demo.hid.hci_ble_hid_dev-CYW920719Q40EVB_01 download 
    Linking target ELF
    OK, made elf.
    ..\..\43xxx_Wi-Fi\tools\ARM_GNU\Win32\bin\arm-none-eabi-objdump: section '.ordered' mentioned in a -j option, but not found in any input file
    ..\..\43xxx_Wi-Fi\tools\ARM_GNU\Win32\bin\arm-none-eabi-objdump: section '.aon' mentioned in a -j option, but not found in any input file
    ..\..\43xxx_Wi-Fi\tools\ARM_GNU\Win32\bin\arm-none-eabi-objdump: section '.pram_rodata' mentioned in a -j option, but not found in any input file
    Call to hci_ble_hid_dev_spar_crt_setup @ 00216b79
    OK, made C:/Users/Yajun Wu/Documents/WICED-Studio-6.2/20719-B1_Bluetooth/WICED/../build/hci_ble_hid_dev-CYW920719Q40EVB_01-rom-ram-Wiced-release/A_20719B1-hci_ble_hid_dev-rom-ram-spar.cgs. MD5 sum is:
    97486653cedb195160769bcf557dd262 *../build/hci_ble_hid_dev-CYW920719Q40EVB_01-rom-ram-Wiced-release/A_20719B1-hci_ble_hid_dev-rom-ram-spar.cgs
    
    --------------------------------------------------------------------------------
    Patch code starts at              0x00270400 (RAM address)
    Patch code ends at                0x0027ACD8 (RAM address)
    Patch RW/ZI size                  2936 bytes
    Application starts at             0x00215768 (RAM address)
    Application ends at               0x00216B78 (RAM address)
    
    Patch code size                        43224 bytes
    Application RAM footprint               5136 bytes
                                          ------
    Total RAM footprint                     8072 bytes (7.9kiB)
    --------------------------------------------------------------------------------
    
    Converting CGS to HEX...
    Conversion complete
    
    Creating OTA images...
    Conversion complete
    OTA image footprint in NV is 54918 bytes
    
    Detecting device...
    Device found
    
    
    Downloading application...
    Download complete
    
    Application running.
    
    23:41:08 Build Finished (took 17s.205ms)

If the error code is device unfound, first thing to do is make sure your device manager has showed the serial ports. Then the next possible solution is that you need to reboot your board to make it into a downloadable situation. Press the reset button first, then press the recovery button, release the reset first, then release the recovery. Your board would be reboot after this action.  Then try double click the target again.           

Client Control is a windows software made by WICED. It has so many functions, today we just use it to test out HID device, to see if our modified version is rightly work.  We could use it to send code to our Bluetooth board and it would tell the computer how to move the curse.

You would find Client Control in the following path: apps->host->client_control->Windows->ClientControl.exe. Double click to open it.

 





Then choose the right port and right baud rate: 30000, then you open the port. If work right, you would see the log information in the console under the bottom. Then find the page in the middle to find the HID Device. You would see it in my screenshot. Then you would click the Enter Pairing Mode to try to control your Bluetooth device. Now, you would use another computer or just use this computer to see if you would control the mouse by sending the report. For example:01 00 20 20 00 00 00 00 00 ,the curse would move on  right-down direction. If your send 01 01 00 00 00 00 00 00 00, the mouse would hold on clicking condition.

Part 3 Accelerator

Now, we will make the accelerator work for us. The code for controlling accelerator is adding in the hci_ble_hid_dev.c. All the changes are list in the following. Some of the codes are taken from the example about accelerator.

To use accelerator, we need use some variable to mark the movement of our mouse, like x, y, z and mouse click. We add some definition in the begin of our file.

Definitions:

    static UINT8 current_speed;
    static wiced_timer_t seconds_timer;
    
    // relative mouse position
    static int16_t mouse_x_position;
    static int16_t mouse_y_position;
    static int16_t mouse_z_position;
    
    static uint16_t report_count;
    static int32_t x_sum;
    static int32_t y_sum;

Then, that are some useful constant value for our function in the Macros part:

    #define CLK_FREQ 24000 /*Clock frequency in KHz*/
    #define LSM9DS1_ACC_GYRO_I2C_ADDRESS (0xD4 >> 1) /*Motion sensor slave address*/
    #define LSM9DS1_ACC_GYRO_WHO_AM_I_REG 0X0F /*WHO_AM_I register address*/

This is the Function implementations:

    void mouse_i2c_initialize( void )
    {
        wiced_hal_i2c_init();
        uint8_t status;
    
        //Turn on Accelerometer - Register 0x20... 2g accelerometer on @ 50hz
        uint8_t data[] = {0x20,0x40};
        status = wiced_hal_i2c_write(data,sizeof(data),LSM9DS1_ACC_GYRO_I2C_ADDRESS);
    
        current_speed = wiced_hal_i2c_get_speed();
    
        WICED_BT_TRACE("Default I2C speed: %d KHz\n\r", (CLK_FREQ/current_speed));
    
        /*Start a timer for POLL_TIMER seconds*/
    
        if ( WICED_SUCCESS == wiced_init_timer( &seconds_timer, &comboread_cb, 0, WICED_MILLI_SECONDS_PERIODIC_TIMER )) {
            if ( WICED_SUCCESS != wiced_start_timer( &seconds_timer, 40 )) {
                WICED_BT_TRACE( "Seconds Timer Error\n\r" );
            }
        }
    }
    
    /******************************************************************************
     * This function reads the value from I2C slave and prints it
     *****************************************************************************/
    wiced_bool_t key_is_pressed = WICED_FALSE;
    void comboread_cb (uint32_t arg)
    {
        UINT8 status = 0xFF;
        UINT8 reg_add = 0x28; // Accelerometer register
    
        typedef struct {
            int16_t ax;
            int16_t ay;
            int16_t az;
        } __attribute__((packed)) accel_val_t;
    
        accel_val_t data;
        status = wiced_hal_i2c_combined_read((UINT8 *)&reg_add, sizeof(UINT8), (uint8_t *)&data, sizeof(data), LSM9DS1_ACC_GYRO_I2C_ADDRESS);
    
        if(I2CM_SUCCESS == status) {
            mouse_x_position -= data.ax;
            mouse_y_position -= data.ay;
            x_sum += mouse_x_position;
            y_sum += mouse_y_position;
            //mouse_z_position -= data.az;
            uint8_t p_data[] = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00};
            p_data[1] = (mouse_x_position >> 12) & 0xff;
            p_data[2] = (mouse_y_position >> 12) & 0xff;
            uint16_t length = sizeof(p_data);
            report_count++;
            if(report_count % 8 && hci_ble_hid_dev_state.paired == WICED_TRUE && hci_ble_hid_dev_state.host_started_pairing == FALSE ) {
                //
                mouse_x_position = (x_sum << 3) & 0xff;
                mouse_y_position = (y_sum << 3) & 0xff;
                WICED_BT_TRACE("raw=%6d %6d, delta=%4d, %4d, dx=%4d, dy=%4d, ", data.ax, data.ay, mouse_x_position, mouse_y_position, p_data[1], p_data[2]);
                WICED_BT_TRACE ("\r");
                
                // send a notification to host
                wiced_bt_gatt_status_t gatt_status = wiced_bt_gatt_send_notification(
                                hci_ble_hid_dev_state.conn_id,
                                HDLC_HUMAN_INTERFACE_DEVICE_REPORT_INPUT_REPORT_VALUE,
                                length,
                                p_data );
                if (gatt_status != WICED_BT_GATT_SUCCESS)
                    WICED_BT_TRACE("Err: wiced_bt_gatt_send_notification failed %d\n\r", gatt_status);
            }
    
        }else if(I2CM_OP_FAILED == status) {
            WICED_BT_TRACE("I2C comboread operatioin failed\r\n");
        }else if(I2CM_BUSY == status) {
            WICED_BT_TRACE("I2C busy\r\n");
        }else{
            WICED_BT_TRACE("Unknown status from I2C\r\n");
        }
    }

There is the Header files part, as we use the timer and i2c in our code so that we need to add them in our header to help the program finding the reference files:

    #include "wiced_timer.h"
    #include "wiced_hal_i2c.h"

Don't forget to make the declarations for our function!

    void comboread_cb (uint32_t arg);
    void mouse_i2c_initialize();

Finally, we add "mouse_i2c_initialize();" into the init function, be careful you should add this in the very begin of our file:

    mouse_i2c_initialize();

## Part 4 Cursor
continuing
