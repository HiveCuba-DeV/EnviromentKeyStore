# EnviromentKeyStore
# encrypt.py : JSON File Encryption Tool
Creates an Encrypted KeyStore file with the secure assignment of environment variables to be used in Bash

## 🔐 Purpose
A Python utility for securely encrypting JSON files using AES-256-CBC encryption through Fernet symmetric cryptography. Designed for sensitive data protection with secure password-based key derivation.

## 🛠️ Features
- Secure password-based key derivation (PBKDF2-HMAC-SHA256)
- Automatic original file deletion after encryption
- Encrypted output in standardized keystore format
- Cross-platform compatibility
- Comprehensive error handling

## ⚙️ Prerequisites
- Python 3.6+
- cryptography library (`pip install cryptography`)

## 📥 Installation
```bash
git clone https://github.com/yourusername/json-encryptor.git
cd json-encryptor
pip install -r requirements.txt  
```

## 🖥️ Usage
```bash
python encrypt.py input.json keystore.json
```
Sample workflow:
```bash
# Original file
echo '{"secret": "sensitive-data"}' > original.json

# Encryption
python encrypt.py original.json keystore.json
Enter encryption password: ********

# Result
cat keystore.json
{
    "cipher": "gAAAAABm...<encrypted_data>"
}
```

## 🔒 Security Considerations
- The password is not stored - loss = irreversible data loss
- Minimum recommended password: 12+ characters with mixed character types
- Output file contains:
  - AES-encrypted ciphertext
  - HMAC for data integrity verification
  - Timestamp for replay protection

## 🚨 Error Handling
Common error scenarios:
| Error Type | Resolution |
|------------|-------------|
| `FileNotFoundError` | Verify input file path |
| `PermissionError` | Check file/directory permissions |
| `JSONDecodeError` | Validate input JSON syntax |
| `TypeError` | Ensure proper data serialization |

## ⚠️ Important Notes
1. Original file is **permanently deleted** after encryption
2. Output file uses standard JSON format for interoperability
3. Encryption parameters:
   - Key derivation: SHA-256
   - Cipher: AES-256-CBC
   - Encoding: URL-safe base64
4. Test with non-critical files before production use

# decript.py : Keystore Decryptor & Bash Environment Loader
## 🔓 Purpose
Complementary tool to the JSON File Encryption Utility that decrypts encrypted keystore files and converts them into Bash-compatible environment variables using secure password-based decryption.

## 🛠️ Core Functionality
- AES-256-CBC decryption of Fernet tokens
- JSON-to-Bash environment variable conversion
- Secure password handling with hidden input
- Cross-platform shell integration

## ⚙️ Prerequisites
- Python 3.6+
- cryptography package (`pip install cryptography`)
- Bash shell (v4.4+ recommended)

## 🖥️ Basic Usage
```bash
python decrypt.py keystore.json
```
Sample workflow:
```bash
# Decrypt and load variables
eval "$(python decrypt.py keystore.json)"
Enter decryption password: ********

# Verify environment variables
echo $API_KEY
```

## 🔄 Advanced Integration
### Permanent Environment Loading
```bash
# Add to ~/.bashrc
load_keystore() {
    eval "$(python /path/to/decrypt.py $1)"
}

# Usage
load_keystore production.env.enc
```

### Docker Integration
```Dockerfile
FROM python:3.9-slim
COPY decrypt.py .
RUN pip install cryptography

CMD ["sh", "-c", "eval $(python decrypt.py ${KEYSTORE_FILE}) && your-app"]
```

## 🛡️ Security Implementation
| Component          | Security Measure                     |
|--------------------|--------------------------------------|
| Password Handling  | getpass() with hidden input          |
| Key Derivation     | SHA-256 + base64 URL-safe encoding   |
| Data Integrity     | HMAC verification built into Fernet  |
| Memory Management  | Clears sensitive data after use      |

## 🚨 Error Conditions
- **Invalid Password**: Automatically fails with "Invalid token" error
- **Tampered Ciphertext**: HMAC verification failure
- **Expired Tokens**: Temporal validity enforcement (if implemented)

## ⚠️ Critical Usage Notes
1. **Eval Security**: Always validate keystore file integrity before using `eval`
2. **Variable Collisions**: Checks for existing environment variables
3. **Character Escaping**: Automatically handles special characters in values
4. **Session Isolation**: Variables only persist in current shell session
 

## 📜 License
MIT License - Use at own risk for sensitive data



