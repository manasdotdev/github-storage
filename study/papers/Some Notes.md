Some Notes

Step-by-step breakdown of how a server serves files, why renaming them doesn't immediately change what clients see, and how updates work at the system level.

---

### Step 1: The Static State (Files on Your Hard Drive)

* **What it is:** A program or website asset stored on your disk (SSD/HDD) is just a **static file**. It is a passive collection of bytes—zeros and ones sitting on a magnetic platter or flash memory cell.
* **The Folder Index:** When you look at a file manager, the OS shows you file names mapped to physical locations on the disk. Think of this directory index like a library catalog card: it tells the OS where to find the physical pages, but the card itself isn't the story.

---

### Step 2: The Dynamic State (Processes in RAM & The Kernel)

When you tell your computer to run a program or start a server, things change dramatically:

* **The Kernel as Traffic Cop:** The OS **kernel** is the core software layer that talks directly to your hardware. It manages CPU time, system memory (RAM), and security.
* **Creating a Process:** The kernel reads the static file from the disk, loads its instructions and data into **RAM**, and creates a **Process**—a living, breathing instance of that program executing instructions in real time.
* **The File Handle Link:** When the running process needs to read data or serve files, the kernel establishes a direct communication bridge (called a **file descriptor** or file handle) linking the active process in RAM directly to the physical storage sectors on the disk.

---

### Step 3: The Deletion & Renaming Illusion

When you modify, rename, or "delete" a file while the server process is actively running:

* **Unlinking, Not Erasing:** Deleting or renaming a file only removes or changes its entry in the folder index (the "catalog card").
* **The Kernel Holds On:** Because the running process still has an active file handle pointing to the underlying data blocks on the disk, **the kernel refuses to delete the actual data**. It knows a living process depends on it.
* **Why the Old Content Remains:** The server process keeps executing the instructions or serving the content currently loaded in RAM (or directly referenced via its open file handle). It doesn't care that you changed the name in the folder, because its direct hardware link points straight to the original data sectors.

---

### Step 4: The Restart (Cutting the Cord)

To force the system to acknowledge the new file:

1. **Killing the Process:** When you restart the server (or stop the service), the OS terminates the old process.
2. **Releasing the Link:** Once the process dies, the kernel notes that no active processes are using that file handle anymore. It finally seizes the opportunity to unmap the memory and free up those disk sectors.
3. **Starting Fresh:** When the service manager starts a new process, that brand-new process looks up the file name (e.g., `website`), finds the *new* updated version currently sitting at that name, and opens a brand-new link to it.

---

### Step 5: How Servers Update *Without* Restarting (Advanced Architecture)

Production servers often cannot afford downtime to restart. To update code or files on the fly without dropping user connections, developers use advanced OS and server mechanisms:

* **Signals (like SIGHUP):** Unix-based processes can listen for specific operating system "signals." When you send a `SIGHUP` signal to a server, it catches the signal, gracefully re-reads its configuration or reloads its binary file into memory on the fly without killing active network connections.
* **Master/Worker Handoffs:** Advanced servers use a **Master process** that manages multiple **Worker processes**. When an update arrives:
1. The master process spins up new worker processes running the *new* code.
2. The old workers finish processing whatever requests they are currently handling.
3. Once old workers finish their active tasks, they exit cleanly, leaving only the new workers behind—achieving a seamless, zero-downtime update.