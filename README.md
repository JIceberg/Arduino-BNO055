# Arduino-BNO055

A library for the BNO055 IMU for Arduino. This is based on [Kris' BNO055 Repository](https://github.com/kriswiner/BNO055), but heavily simplified.

This was written for [HyTech Racing](https://www.github.com/hytech-racing/).

## Features
There are currently 5 supported data modes, represented with the enum `DataMode`.
* GYRO    - angular velocity
* GRAVITY - gravitational acceleration
* ACCEL   - accelration including movement
* EUL     - absolute angular orientation
* QUAT    - absolute orientation in quaternion form

There is an `AxisData` union that is used to store data retrieved from the IMU.
* `accelAxes[3]`
* `gyroAxes[3]`
* `gravAxes[3]`
* `eulerAngles[3]`
* `quaternions[4]`

However, this is the raw data from the register. To obtain useful information, you will need to manipulate the values.
* For the `accelAxes` and `gravAxes`, leave the data as it is obtained
* To get the radians / second of the gyroscope, divide the `gyroAxes` data by 16
* To get the absolute angular orientation from `eulerAngles`, divide the data by 16
* The `quaternions` data should be divided by 16384

## Usage
To use the library, simply add the header file as an include. Then, in your `setup()` function, begin the `Wire`to communicate with I2C. Then, initialize the IMU with `imu_init()`.
```ino
#include <BNO055_IMU.h>

void setup()
{
  Wire.begin();
  imu_init(); // initializes the IMU
}
```

To read data from the IMU, simply call `read_data(*data, mode)` with an `AxisData` destination called `data`. An implementation of this would be the following:
```ino
void loop()
{
  AxisData *data;
  if (!(data = (AxisData *)malloc(sizeof(AxisData)))) {
      return;
  }

  read_data(data, ACCEL);
  
  free(data);
}
```
To make this data useful, apply the manipulation suggested earlier. For example, for the quaternion data
```ino
q.x = (float)(data->quaternions[0]) / 16384.;
q.y = (float)(data->quaternions[1]) / 16384.;
q.z = (float)(data->quaternions[2]) / 16384.;
q.w = (float)(data->quaternions[3]) / 16384.;
```
