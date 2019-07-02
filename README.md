# C# communicate with USB-HID
Following these steps  
1.In your project open nuget, search and install Hid.Net,Usb.Net  
2.Init device  
```c#
            uint vid = 0x0483, pid = 0xA19B;
            //Register the factories for creating Usb devices. This only needs to be done once.
            WindowsUsbDeviceFactory.Register();
            WindowsHidDeviceFactory.Register();

            //Define the types of devices to search for. This particular device can be connected to via USB, or Hid
            var deviceDefinitions = new List<FilterDeviceDefinition>
            {
                new FilterDeviceDefinition{ DeviceType= DeviceType.Hid, VendorId= vid, ProductId=pid },
                //new FilterDeviceDefinition{ DeviceType= DeviceType.Usb, VendorId= vid, ProductId=pid },
                //new FilterDeviceDefinition{ DeviceType= DeviceType.Usb, VendorId= 0x1209, ProductId=0x53C0, Label="Model T" }
            };

            //Get the first available device and connect to it
            var devices = await DeviceManager.Current.GetDevicesAsync(deviceDefinitions);
            var trezorDevice = devices.FirstOrDefault();
            if (trezorDevice == null)
                info.Text = "not found device";
            else
            {
                info.Text = trezorDevice.DeviceId;
            }
            await trezorDevice.InitializeAsync();
```
3.write info to device  
```c#
            //Get the first available device and connect to it
            var devices = await DeviceManager.Current.GetDevicesAsync(deviceDefinitions);
            var trezorDevice = devices.FirstOrDefault();
            if (trezorDevice == null)
                //info.Text = "not found device";
            else
            {
                //info.Text = trezorDevice.DeviceId;
            }
            await trezorDevice.InitializeAsync();

            //Create a buffer with 3 bytes (initialize)
            byte[] pa4 = new byte[7]
            {
                0x55,0xAA,0x04,0x04,0xFF,0x01,0x01
            };

            var buffer = new byte[64];
            buffer[0] = 0x3f;
            buffer[1] = 0x23;
            buffer[2] = 0x23;

            //Write the data to the device
            await trezorDevice.WriteAsync(pa4);
        }
 ```
 4. read info from device  
 ```c#
             //Read the response
            var readBuffer = await trezorDevice.ReadAsync();
            var readstr = UTF8Encoding.UTF8.GetString(readBuffer);
            // info.Text = readstr;
 ```
 
 The info is TextBlock control in wpf in my code.
