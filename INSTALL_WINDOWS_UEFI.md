# Guide: Installing Windows on UEFI Mode / GPT

This guide outlines the process for installing Windows in **UEFI mode** with **GPT partitioning**. If you're using Windows 7 or Vista, you can use **ImageX** instead.

## Prerequisites
- A Windows Bootable USB Installer or Windows PE environment.
- A machine with **UEFI** enabled in the BIOS/UEFI settings.
- The `install.wim` or `install.esd` file from your Windows installation media.
- **ImageX** (for Windows 7 or earlier versions) or **DISM** (for Windows 8 and later).

---

## Steps to Install Windows in UEFI Mode with GPT

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

### 6. Convert to GPT Partition Scheme
- Convert the disk to GPT (GUID Partition Table) style.

    ```bash
    convert gpt
    ```

### 7. Create the EFI System Partition
- Create a 512 MB EFI system partition (required for UEFI boot).

    ```bash
    create partition efi size=512
    ```

### 8. Format the EFI Partition
- Format the EFI partition with the FAT32 filesystem.

    ```bash
    format quick fs=fat32
    ```

### 9. Assign a Letter to the EFI Partition
- Assign a drive letter (e.g., `Z:`) to the EFI partition.

    ```bash
    assign letter=Z
    ```

### 10. Create a Primary Partition
- Create a primary partition for the Windows installation.

    ```bash
    create partition primary
    ```

### 11. Format the Primary Partition
- Format the primary partition quickly.

    ```bash
    format quick
    ```

### 12. Assign a Letter to the Primary Partition
- Assign a drive letter (e.g., `W:`) to the primary partition.

    ```bash
    assign letter=W
    ```

### 13. Exit DiskPart
- Exit the disk partition tool.

    ```bash
    exit
    ```

---

## Apply the Windows Image

### 14. Locate the `install.wim` or `install.esd`
- Navigate to the USB drive containing the Windows installation files (adjust the drive letter accordingly).

    ```bash
    D:
    cd sources
    ```

### 15. Get Image Information
- Check the available Windows editions in the `install.wim` or `install.esd` file.

    For **DISM** (Windows 8 and later):

    ```bash
    dism /get-imageinfo /imagefile:install.wim
    ```

    For **ImageX** (Windows 7 or earlier):

    ```bash
    imagex /info install.wim
    ```

- If you have an `install.esd` file, use this command instead for **DISM**:

    ```bash
    dism /get-imageinfo /imagefile:install.esd
    ```

### 16. Select the Edition to Install
- Identify the edition you want by checking the index number in the output from the previous command.

### 17. Apply the Windows Image
- Apply the selected image (replace `X` with the edition index number).

    For **DISM** (Windows 8 and later):

    ```bash
    dism /apply-image /imagefile:install.wim /index:X /applydir:W:
    ```

    Or, for **ImageX** (Windows 7 or earlier):

    ```bash
    imagex /apply install.wim X W:
    ```

### 18. Set Up the Bootloader
- Use `bcdboot` to set up the boot files for UEFI.

    ```bash
    bcdboot W:\Windows /s Z:
    ```

### 18. Set Up the Bootloader
- Use `bcdboot` to set up the boot files for UEFI.

    ```bash
    bcdboot W:\Windows /s Z:
    ```

---

## Conclusion
You have now successfully installed Windows in **UEFI mode** with **GPT partitioning**. Restart your computer, and Windows should boot up from the newly installed disk.

If you encounter any issues or need more help, feel free to consult the [official Microsoft documentation](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/bcdboot-command-line-options).
