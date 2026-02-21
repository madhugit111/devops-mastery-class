# Docker Interview Q&A

## Why does Docker Desktop require virtualization?

Docker runs Linux containers.

On Windows:
- Linux kernel is required
- Linux kernel runs inside WSL2
- WSL2 uses Hyper-V
- Hyper-V requires CPU virtualization (SVM / VT-x)

Without virtualization:
Hyper-V cannot start
→ WSL2 cannot run
→ Docker Engine fails

Therefore, Docker Desktop requires virtualization enabled.