# Dead / no-boot server: find the EARLIEST failing stage, narrate as you go

### 1. Stage 1 Power:
    - Every Power connection seated?
    - Fans Spinning?
    - PDU connection verified?
    - Rack A/B feeds sources live and verified?
    - PSU lights on? Amber lights?
    - No fans or lights = Power delivery issue or a toasted motherboard

### 2. Stage 2 POST
    - Power On Self Test
    - Firmware checks
    - BIOS Recovery
    - Isolate components of the motherboard
    - Remove all accessory Mobo components (e.g. Graghics cards, fans, PCIE slots)
    - Populate one DIMM slot at a time to troubleshoot ram
    - Check CPU (Bent pins)
    - Pinpoint failing piece of hardware
    - Beeping and flashing code(POST Code), refer to Manufature's docs
    - IPMI (Intillegent Platform Management Interface) Open standard protocol for BMC, Out-Of-Band Monitoring tool used to POST snoop and and report finding to Baseboard Management Controller

### 3. Boot hand-off
    - No boot device found = Hardware cleared
    - Dead boot drive
    - Failed RAID
    - Disconnected Backplane (Connection between raid controller and drives)
    - Check logs

### 4. OS Load
    - Hanging
    - Kernal Panic
    - Bad device drivers
    - Corrupted files or filesystem
    - BSOD
    - Check logs

## IPMI, BMC

### IPMI
- Intellegent Platform Management Interface
- Open standard proticol for Out-of-Band monitoring
- Checks Service Event Logs (SEL) and power cycles server

### BMC
- Baseboard Management Controller
- Independent computer on motherboard to allow for OOB monitoring
- Reads temps, fans, voltage
- POST snoops motherboard
- Writes hardware events to SEL

### When to use it: 
first stop for any "server down" ticket. Check the SEL and power state before walking to the rack, and confirm whether it actually POSTed.

## IPMI Commands
- ``ipmitool power status``: Is the server on/off
- ``ipmitool power on/off/cylce/reset``: Powers on/off cycle or reset server
- ``impi sel elist``:
    - sel: System event logs
    - e: extented (easy to read)
    - list: list componets
    - All together it will list the temps and states of all Mobo components (Ram, CPU, power supply fans etc)
- ``impi sdr list``
    - SDR (Sensor Data Record)
    - list
    - All together it will list live senor readings (CPU temp, fan speeds, etc)
    - ``ipmi fru print``
        - fru (field replaceable unit)
        - print
        - All together it will print the ID component of the server's main parts

## Hot Swap vs Cold Swap componets

### Hot Swap
- Components that can be swapped when the server is on
    - PSU's
    - Fans in redundant setup
    - Drives

### Cold Swap
- Componets where the server needs to be powered off before it is swapped
    - CPU
    - Ram
    - PCIE cards
    - Most Nics
    - Raid controllers
    - Graphics card


## RAID

| Raid level | What is it | Survives | Notes |
| ---------- | ---------- | -------- | ------| 
| 0   | Striping for speed | 0 failures | One disk dies = lose everything |
| 1 | Mirroring (2 Disks) | 1 failure | Writes at half the speed, storage is N - 1
| 5 | Striping with Parity | 1 failure |  • Writes data + parity across all disks; minimum 3 disks<br>• Usable storage = (N − 1) × smallest drive | 
| 6 | Striping with double parity | 2 failures | Writes data + parity across all disk; minumum 4 disk<br>• Usable storage = (N - 2) x Smallest drive | 
| 10 | Mirrored Pairs, then striped | 1 per mirror pair |  • Speed + Redundancy<br>• Needs 4+ disk | 

### Degraded
- A disk failed, array survived data, a rebuild will is nessessary to restore RAID Array. Leaves ARRAY vunerable

### Rebuild
- Swap in a new disk to reconstruct Raid array


