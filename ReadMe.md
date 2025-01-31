# **Building FFTW for Bare-Metal Zynq**
PLease **Fork** and **Star** if you really use it in your design.  
Best regards.  

This guide provides step-by-step instructions on how to build FFTW for **bare-metal** execution on the **Xilinx Zynq SoC**.
There are three main libraries that can be used in zynq:    
1- **CMSIS-DSP**   
2- **NE10**  
3- **FFTW**  
Before that, there are other libraries that can be used on Zynq, each with its own pros and cons. For example, **CMSIS-DSP** supports a maximum FFT size of 4K points, while **NE10** only supports FFT sizes that are powers of 2 (2^N). In contrast, **FFTW** offers high scalability and supports arbitrary FFT sizes.
## Comparison: FFTW vs. CMSIS-DSP vs. NE10

| Library       | Precision       | Platform                      | SIMD Optimized | Fixed-Point Support | Best Use Case |
|--------------|----------------|-------------------------------|----------------|---------------------|---------------|
| **FFTW**     | Float, Double   | General-purpose (Linux/Bare-metal) | ✅ Yes (NEON) | ❌ No | High-accuracy FFT on embedded systems |
| **CMSIS-DSP** | Float, Q15, Q31 | Cortex-M/A (Microcontrollers & Zynq) | ✅ Yes (NEON) | ✅ Yes | Low-power embedded DSP (MCUs, real-time FFT),Less than 4k fft point and many other functions such as FIR and many kind of filtering|
| **NE10**     | Float, Fixed    | ARM Cortex-A (Zynq, mobile)  | ✅ Yes (NEON) | ✅ Yes | High-performance FFT on ARM processors, Only power of 2 FFT point |

### **Which One Should You Use?**
- **For general-purpose ARM-based FFT (bare-metal or Linux on Zynq)** → **FFTW**
- **For microcontrollers or low-power DSP tasks** → **CMSIS-DSP**
- **For high-performance ARM Cortex-A applications** → **NE10**


---

## **Prerequisites for building FFTW**

### **For Ubuntu/Linux Users**
Install the required packages:
```bash
sudo apt update
sudo apt install -y gcc-arm-none-eabi make autoconf libtool
```

### **For Windows Users**
If using **WSL**, install the same packages as above.  
If using **Windows natively**, install the [Xilinx Vitis SDK](https://www.xilinx.com/products/design-tools/vitis.html), which includes the **ARM cross-compilation toolchain**.

---

## **Step 1: Download FFTW Source Code**
```bash
wget http://www.fftw.org/fftw-3.3.10.tar.gz
tar -xvzf fftw-3.3.10.tar.gz
cd fftw-3.3.10
```

---

## **Step 2: Set Up the Cross-Compiler**
For being sure that the compiler have been installed. 
```bash
sudo apt install --reinstall gcc-arm-none-eabi
```

---

## **Step 3: Configure FFTW for ARM Cortex-A9**
Run the following command to configure FFTW for **bare-metal execution**:   
**NOTE:**Remember to change **--prefix=/home/milad/build** to your address.
```bash
./configure \
    --host=arm-none-eabi \
    --enable-float \
    --enable-neon \
    --disable-threads \
    --enable-static \
    --disable-shared \
    --prefix=/home/milad/build \
    CFLAGS="-O3 -mcpu=cortex-a9 -mfpu=neon -mfloat-abi=hard -ffast-math -ffreestanding -DFFTW_NO_TIMER" \
    LDFLAGS="-lm --specs=nosys.specs"  
```  
## **Step 4: Configure FFTW for ARM Cortex-A9**  
```bash
make
make install
```  
## **Step 5: Adding FFTW to you project **  
Create a folder in your project and name it FFTW (or any name you prefer), then copy the files from the build directory into it, as illustrated in the following figures.  
![Adding FFTW pic](imag/1.png)  


## **Step 6: Setting the Linker and adding the library to your compiler **  
Add the Library to Your Vitis (SDK) Project
Open Xilinx Vitis or SDK and select your project.  
Right-click your project → Click Properties.  
Navigate to C/C++ Build → Settings.  
Under ARM GNU Toolchain → Select Libraries.  
Add the following paths:
Library search path (-L) as depicted:  
![Linker](imag/2.png)  


## **Step 6: Add Library Path **    
To make the Xilinx SDK aware of the library path, follow these steps:

Open Xilinx SDK and your project.
In the Project Explorer,  
 right-click on your project and select Properties.  

Navigate to C/C++ General > Paths and Symbols.  

Under the Libraries tab:  

Click Add External Library to add an external library or Add Library to add a library already available in the workspace.
You can add the path to the library in the Library Search Path section (under Include paths). As depicted:![Path and symbol](imag/3.png)    

## **Step 7: Test Sample code** 
After running this code you will face an error: I will explain how to solve it.  
**Note:** Remember to use fftwf since it is 32 bit floating point. 
```bash
#include <fftw3.h>
#include <stdio.h>

int main() {
    int N = 8;
    fftwf_complex in[N], out[N];
    fftwf_plan p;

    p = fftwf_plan_dft_1d(N, in, out, FFTW_FORWARD, FFTW_ESTIMATE);

    // Set input values
    for (int i = 0; i < N; i++) {
        in[i][0] = i;  // Real part
        in[i][1] = 0;  // Imaginary part
    }

    fftwf_execute(p);
    fftwf_destroy_plan(p);

    // Print output
    for (int i = 0; i < N; i++)
        printf("out[%d] = %f + %fi\n", i, out[i][0], out[i][1]);

    return 0;
} 
```  
![Path and symbol](imag/4.png)   

## **Step 8: Solve the problem and enjoy**    
Creating a dummy function for **_gettimeofday** at the top of your code and try building it again. 
```bash
void _gettimeofday(void){}; 
```  
## **Step 9: Enjoy*
ENJOY !!!!!!!!!!!!!!!   
ENJOY !!!!!!!!!!!!!!!   
ENJOY !!!!!!!!!!!!!!!  
ENJOY !!!!!!!!!!!!!!!  

PLease **Fork** and **Star** if you really use it in your design.
Best regards.


## Author
M.Mohmmadi.B  
Mr.Kolahiyan  
Mr.Barazesh   
M.M.Sayadi  
