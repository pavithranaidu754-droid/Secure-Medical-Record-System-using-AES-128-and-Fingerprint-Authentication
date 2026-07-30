# Secure Medical Record System using AES-128 and Fingerprint Authentication

##  Overview
This project implements a secure medical record system that combines AES-128 encryption with fingerprint-based authentication to ensure that only authorized users can access sensitive patient information.

##  Problem Statement
Medical records contain highly sensitive information that must be protected from unauthorized access. Traditional password-based authentication is vulnerable to attacks and credential theft. This project enhances security by integrating biometric authentication with AES-128 encryption.

##  Objectives
- Encrypt medical records using AES-128.
- Authenticate users through fingerprint recognition.
- Ensure confidentiality and integrity of patient data.
- Demonstrate a hardware-oriented implementation suitable for FPGA and embedded systems.

## ⚙️ Technologies Used
- Verilog HDL
- Python
- Xilinx Vivado
- AES-128 Cryptography

## Project Architecture

```
Fingerprint Scan
        │
        ▼
Fingerprint Authentication
        │
        ▼
AES-128 Encryption
        │
        ▼
Encrypted Medical Record
        │
        ▼
Authorized User
        │
        ▼
Medical Record Display
```

## ✨ Features
- AES-128 Encryption with novelty of key derivation 
- Before and after AES Whitening techniques
- Fingerprint Authentication
- Secure Medical Record Storage
- FPGA-Friendly Design
- High Data Security

##  Repository Structure

```
.
├── README.md
├── verilog/
├── python/
├── images/
```

