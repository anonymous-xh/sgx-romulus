## Purpose of sgx-romulus
- The aim of `sgx-romulus` project is to develop an sgx-compatible persistent memory library. The `sgx-romulus` library was ported from `romuluslog` library.
- The project demonstrates how to use PMEM from within an sgx enclave.The untrusted part of the application is the `App` folder while the trusted part is the `Enclave` folder.

## How sgx-romulus works
- Sgx-romulus has 2 components: one untrusted component: `Romulus_helper.cpp` which initializes the pmem and does the memory mapping of the pmem file (`/dev/shm/romuluslog_shared`) into the application's VAS via the `rom_init` routine; the trusted component of sgx-romulus is the folder `Enclave/romulus` folder. 
- Once the memory mapping is done in the untrusted runtime, the application calls the `ecall_init` routine to initialize the trusted romulus instance with the memory mapped portion and persistent header information. Once that is done, sgx-romulus is ready to create and manipulate persistent data structures.
- It should be noted that pmem is manipulated inside the enclave but the memory mapped area is not in enclave memory, so for confidentiality all data written to pmem should be encrypted within the enclave; Encryption functionality can easily be added to the code. 
- Under normal circumstances, an enclave exit is not necessary to correctly manipulate pmem within the enclave except for special ocalls e.g following an abort. 
- The romulus log is allocated in enclave memory.
- On application termination the ocall `my_ocall_close` does the `munmap` of the pmem file.

### [![Generic badge](https://img.shields.io/badge/<Status>-<Dev>-<COLOR>.svg)]()
- At the moment sgx-romulus is still in development mode but is already largely functional and can be used for an sgx/pmem project. Confidentiality of persistent data using encryption is enough when encryption is done but work still needs to be done to improve the overall integrity of the persistent memory metadata.


## Example project
- The example application in this project creates a persistent stack on pmem from inside the enclave. Multiple threads are created outside the enclave and they all 
push and pop data to/from the stack. Each thread-id is printed for the push/pop. 
- Other thread synchronization related messages are printed just for debugging purposes but will be removed later.

## How to Build/Execute the code
- Install Intel(R) SGX SDK for Linux* OS
- Build the project with the prepared Makefile:
    ### Hardware Mode, Debug build:    
        - $ make SGX_MODE=HW SGX_DEBUG=1
        
    ### Hardware Mode, Pre-release build:
        - $ make SGX_MODE=HW SGX_PRERELEASE=1
    ### Hardware Mode, Release build:
        - $ make SGX_MODE=HW

- Execute the binary directly:
    ```$ ./app```
- The code can be debugged with `sgx-gdb` debug tool.

## How to integrate sgx-romulus in your Intel SGX project
- The simplest way to do this is to use this project as the initial template of your SGX application.
- Then write your SGX program accordingly following the exact same SGX SDK rules.

 