# Linux OS Constraint-Driven Simulation

An implementation of Conceptual Data Modeling (CDM) as a constraint-driven system simulation for Linux operating system behavior in language model conversations.

## Overview

This project implements a methodology for simulating Linux system behavior through constraint networks derived from conceptual data models. Rather than using predefined response templates, the system generates behavior through constraint satisfaction and propagation across entity relationships.

### Core Approach

Traditional simulation: Command → Predefined Response → Output  
This implementation: Command → Constraint Activation → Constraint Satisfaction → Generated Response

## Table of Contents

- [Background](#background)
- [Installation](#installation)  
- [Usage](#usage)
- [Architecture](#architecture)
- [Constraint Model](#constraint-model)
- [Examples](#examples)
- [Development](#development)
- [License](#license)

## Background

This implementation is based on the specification "Using Conceptual Data Modeling as Constraint-Driven System Simulation for Language Models", which proposes treating CDMs as executable behavioral specifications. The approach enables language models to simulate system behavior through constraint navigation rather than template matching.

## Installation

No installation is required. The system operates as a prompt-based simulation within language model conversations.

### Prerequisites

- Access to a language model capable of following complex system prompts
- Basic understanding of Linux command-line operations
- Familiarity with conceptual data modeling (recommended)

## Usage

### Starting a Simulation

1. Load the simulation prompt from `linux-cdm-simulation.md`
2. Initialize a conversation with the language model using the prompt
3. Interact using standard Linux commands

### Basic Interaction

```bash
$ pwd
/home/developer

$ ls -la
total 32
drwxr-xr-x 4 developer developer 4096 Jun 19 10:00 .
drwxr-xr-x 3 root      root      4096 Jun 10 08:00 ..
-rw-r--r-- 1 developer developer  220 Jun 10 08:00 .bash_logout
-rw-r--r-- 1 developer developer 3771 Jun 10 08:00 .bashrc
drwx------ 2 developer developer 4096 Jun 19 09:30 .cache
-rw-r--r-- 1 developer developer  807 Jun 10 08:00 .profile
drwxr-xr-x 2 developer developer 4096 Jun 19 10:00 projects
```

### Session Example

```
User: ps aux | grep python
System: $ ps aux | grep python
developer  1823  0.5  2.3 145632 47892 pts/0    S+   10:15   0:02 python3 server.py
developer  2104  0.0  0.0  12784   972 pts/1    S+   10:23   0:00 grep python

User: kill 1823
System: $ kill 1823

User: ps aux | grep python
System: $ ps aux | grep python
developer  2107  0.0  0.0  12784   968 pts/1    S+   10:23   0:00 grep python
```

## Architecture

### Conceptual Data Model

The system is built on a CDM comprising the following primary entities:

#### Core Entities

- **Process**: System processes with attributes including PID, state, priority, memory usage, and parent-child relationships
- **User**: System users with UID, groups, home directories, and ownership relationships
- **File**: Files and directories with paths, permissions, ownership, and content
- **FileSystem**: Mounted filesystems with space constraints and inode management
- **Service**: System services with dependencies and state management
- **Package**: Software packages with version control and file provisioning
- **Device**: Hardware devices and their filesystem representations
- **Signal**: Inter-process communication signals

#### Entity Relationships

- User → Process (ownership)
- Process → File (access)
- Process → Process (parent-child hierarchy)
- File → FileSystem (residence)
- Service → Process (management)
- Package → File (provisioning)

### Constraint Network

The CDM is transformed into a constraint network with four types of constraints:

1. **Generative Constraints**: Define when new entities or states can be created
2. **Coupling Constraints**: Establish relationships between entity behaviors
3. **Invariant Constraints**: Maintain system consistency rules
4. **Goal Constraints**: Guide system behavior toward desired states

## Constraint Model

### Process Constraints

```yaml
lifecycle:
  - fork(): Creates child process when resources available
  - exec(): Replaces process image with executable file
  - exit(): Transitions to zombie state awaiting parent collection
  
resource_management:
  - Memory allocation bounded by system limits
  - CPU scheduling based on priority and nice values
  - File descriptor inheritance through fork()
```

### File System Constraints

```yaml
access_control:
  - Permission checks: user → group → other
  - Effective UID/GID determines access rights
  - Directory execute permission required for traversal

consistency:
  - Path uniqueness within filesystem
  - Inode allocation within filesystem limits
  - Space usage cannot exceed filesystem capacity
```

### Signal Propagation

```yaml
delivery:
  - Signals queue in process signal mask
  - Handler execution follows signal priority
  - SIGKILL and SIGSTOP cannot be caught or ignored
  
hierarchy:
  - SIGCHLD propagates to parent on child state change
  - Process groups receive signals collectively
  - Session leaders propagate certain signals
```

## Examples

### Process Management

```bash
# Fork and exec pattern
$ sleep 30 &
[1] 3245
$ jobs
[1]+  Running                 sleep 30 &
$ kill %1
[1]+  Terminated              sleep 30
```

### File Operations with Permission Checking

```bash
# Create file with restricted permissions
$ touch sensitive.txt
$ chmod 600 sensitive.txt
$ ls -l sensitive.txt
-rw------- 1 developer developer 0 Jun 19 10:30 sensitive.txt

# Attempt access as different user
$ sudo -u nobody cat sensitive.txt
cat: sensitive.txt: Permission denied
```

### Service Management

```bash
# Check service status
$ systemctl status sshd
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled)
   Active: active (running) since Thu 2025-06-19 08:00:00 UTC; 2h 30min ago
   
# Restart service (requires privileges)
$ systemctl restart sshd
Failed to restart sshd.service: Access denied
$ sudo systemctl restart sshd
$ 
```

## Development

### Extending the Model

To add new Linux subsystems:

1. Define entities in the CDM
2. Establish relationships with existing entities
3. Create constraints that govern behavior
4. Test emergent behavior through simulation

### Constraint Definition Language

Constraints follow this general pattern:

```
CONSTRAINT_TYPE: Subject VERB Object WHERE Condition
```

Example:
```
GENERATIVE: Process.memory ALLOCATES space WHERE available_memory >= requested
```

### Testing

Validation involves:
1. Constraint coverage analysis
2. Behavioral consistency verification
3. State coherence across extended interactions

## Limitations

- Simulation accuracy depends on constraint completeness
- Real-time aspects are approximated through conversation
- Hardware-specific behaviors are abstracted
- Kernel internals are simplified to observable behaviors

## References

- "Using Conceptual Data Modeling as Constraint-Driven System Simulation for Language Models" (specification document)
- Linux kernel documentation
- POSIX.1-2017 specification
- SystemD documentation

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Authors

Implementation based on the CDM constraint-driven simulation specification, adapted for Linux operating system behavior modeling.
