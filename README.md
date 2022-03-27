# OpenCL-Device-Helper
A minimum OpenCL platform and device helper to shorten your OpenCL workflow in C++.
This utility helps you quickly get a `cl_device_id` either from keyboard input or with a vendor preference list. It also helps to calculate suitable `global_work_size` and `local_work_size` for `clEnqueueNDRangeKernel` with your `work_dim`, `work_item_size` and a specific `cl_device_id`.

## Quick Start
1. Include cl_device_helper.h in your project.
2. Create a `CLDeviceHelper` instance: `CLDeviceHelper helper;`
3. Call `helper.Initialize();` to initialize the helper. The helper will query and store information about all platforms and their devices on your computer.
4. Get your desired `cl_device_id` in either way below:
  - With a vencor preference list: `auto device_id = helper.GetDeviceIdWithPreference(preference_lower);`
    - A vendor preference list (whose type is `VendorPreferenceList`, an alias for `std::vector<const char*>`) contains vendor name keywords in lower case. The keyword with lower index will have higher priority. The helper will first seek for GPU in order of the list, then other device types in order of the list. If no device contains any of the keywords given, the first device on your computer will be returned.
    - We have six pre-defined preference lists available:
      - `CLDeviceHelper::kPreferenceANI` (AMD > Nvidia > Intel)
      - `CLDeviceHelper::kPreferenceNAI` (Nvidia > AMD > Intel)
      - `CLDeviceHelper::kPreferenceAIN` (AMD > Intel > Nvidia)
      - `CLDeviceHelper::kPreferenceNIA` (Nvidia > Intel > AMD)
      - `CLDeviceHelper::kPreferenceIAN` (Intel > AMD > Nvidia)
      - `CLDeviceHelper::kPreferenceINA` (Intel > Nvidia > AMD)
    - Or you can omit the preference parameter to use the default (`CLDeviceHelper::kPreferenceANI`)
    
  - From keyboard input:
    - Call `helper.PrintAllDevices();` to print all platforms and their devices found on your computer to `cout`.
    - Call `auto device_id = GetDeviceIdFromInput();` to read a platform id(index) and a device id(index) from `cin` to specify a `cl_device_id`. This function is carefully designed to handle various inputs properly on Windows. We appreciate it if you could help improve it on other OS.

5. Whenever you feel headache about calculating a proper `global_work_size` and `local_work_size` for `clEnqueueNDRangeKernel`:
  - Just call this member of your helper instance `GetSuitableGlobalLocalSize(const cl_device_id device_id,const cl_uint work_dim,const size_t* work_item_size,size_t* global_size_ret,size_t* local_size_ret)`. It will take care of querying the given device's specification, calculate suitable global&local sizes, and store results in `global_size_ret` and `local_size_ret` whose memory must be already allocated.
  - The calculated `local_size_ret` is as much as is possible within the give divice's capability. The calculated element in `global_size_ret` is the minimum multiple of the corresponding element in `local_size_ret` not less than the corresponding element in `work_item_size`.
  - For instance, `work_item_size`=[30, 55], `local_size_ret`=[16, 16], and then `global_size_ret`=[32, 64].
 
#### All public member functions of `CLDeviceHelper` may throw a `std::runtime_error` if an error occurs. You should handle them properly.
 
# Happy Coding!
