# Guide: Installing Windows on MBR Mode

This guide outlines the process for installing Windows in **MBR mode** with MBR partitioning.

## Prerequisites
- A Windows Bootable USB Installer or Windows PE environment.
- A machine with **BIOS** or **Legacy Boot** enabled.
- The `install.wim` or `install.esd` file from your Windows installation media.
- **ImageX** (for Windows 7 or earlier versions) or **DISM** (for Windows 8 and later).

---

## Steps to Install Windows in MBR Mode

### 1. Boot into WinPE or Windows Bootable Installer
- Insert your Windows installation USB and boot from it.

### 2. Open Command Prompt
- Once the Windows Installer loads, press `SHIFT + F10` to open the command prompt.

### 3. Disk Partitioning
- Use `diskpart` to configure the disk.

    ```bash
    diskpart
    ```

### 4. Select the Target Disk
- List all disks available.

    ```bash
    list disk
    ```

- Select the disk you want to install Windows on (replace `#` with your disk number).

    ```bash
    select disk #
    ```

### 5. Clean the Disk
- Clean the selected disk to remove any existing partitions.

    ```bash
    clean
    ```

### 6. Convert to MBR Partition Scheme
- Convert the disk to MBR (Master Boot Record) style.

    ```bash
    convert mbr
    ```

### 7. Create a Primary Partition
- Create a primary partition for the Windows installation.

    ```bash
    create partition primary
    ```

### 8. Format the Primary Partition
- Format the primary partition quickly.

    ```bash
    format quick
    ```

### 9. Assign a Letter to the Primary Partition
- Assign a drive letter (e.g., `Z:`) to the primary partition.

    ```bash
    assign letter=Z
    ```

### 10. Exit DiskPart
- Exit the disk partition tool.

    ```bash
    exit
    ```

---

## Apply the Windows Image

### 11. Locate the `install.wim` or `install.esd`
- Navigate to the USB drive containing the Windows installation files (adjust the drive letter accordingly).

    ```bash
    D:
    cd sources
    ```

### 12. Get Image Information
- Check the available Windows editions in the `install.wim` or `install.esd` file.

    For **DISM**:

    ```bash
    dism /get-imageinfo /imagefile:install.wim
    ```

    Or, for **ImageX** (Windows 7 or earlier versions):

    ```bash
    imagex /info install.wim
    ```

- If you have an `install.esd` file, use this command instead for **DISM**:

    ```bash
    dism /get-imageinfo /imagefile:install.esd
    ```

### 13. Select the Edition to Install
- Identify the edition you want by checking the index number in the output from the previous command.

### 14. Apply the Windows Image
- Apply the selected image (replace `X` with the edition index number).

    For **DISM** (Windows 8 and later):

    ```bash
    dism /apply-image /imagefile:install.wim /index:X /applydir:Z:
    ```

    Or, for **ImageX** (Windows 7 or earlier):

    ```bash
    imagex /apply install.wim X Z:
    ```

### 15. Set Up the Bootloader
- Use `bcdboot` to set up the boot files for MBR.

    ```bash
    bcdboot Z:\Windows /s Z:
    ```

    Alternatively, for **Windows 7 or earlier**, you may need to use the `bootsect` command:

    ```bash
    bootsect /NT52 Z:
    ```

    For **Windows 8 or later** (UEFI), you can use:

    ```bash
    bootsect /NT60 Z:
    ```

---

## Conclusion
You have now successfully installed Windows in **MBR mode**. Restart your computer, and Windows should boot up from the newly installed disk.

If you encounter any issues or need more help, feel free to consult the [official Microsoft documentation](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/bcdboot-command-line-options).
