# Neighborhood Santa-Tracking Service 

## Objective
The Santa-Tracking web service was previously available on a known port but no longer appeared to be running there. The objective was to determine which port the `santa_tracker` service had been reconfigured to use and verify that the service was still active.

## Environment
- Linux host provided in the challenge
- Terminal access
  
## Tools Used
- Linux terminal
- ss
- curl
  
## Understanding the Problem
If a service is running, it must be bound to a network port. Even if the application is not visible in a browser, the operating system still tracks active listening ports and the processes attached to them. The task therefore focused on system visibility rather than exploitation.

## Approach
My approach was to:
1. List active listening TCP ports on the system
2. Identify which port was bound to the `santa_tracker` process
3. Connect to the service on that port to confirm functionality

This mirrors real-world service troubleshooting when applications appear to “disappear” due to configuration changes.

## Commands Used

### 1. Enumerate listening services
To view active TCP listeners and associated processes:

```
ss -tlnp
```

This provides:
- listening state
- local address and port
- process name and PID

Example output format:



The port following the colon represents the service port in use.

### 2. Verify service on identified port
After identifying the port, I confirmed the service was active.

Using curl:

```
curl http://localhost:12321  
```

A valid service response confirmed successful communication.

## Result
- The port used by the Santa-Tracking Service was identified
- Connectivity to the service was verified
- The challenge objective was completed successfully

## Lessons Learned
- `ss` is a powerful tool for identifying running services and bound ports
- Services may move ports without stopping completely
- Basic network enumeration is often sufficient to diagnose “missing” applications




