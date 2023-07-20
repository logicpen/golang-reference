## Terminology
* __Plaintext:__ Data that to be converted into a format that's unreadable by other except the person that holds the secret.
* __Ciphertext:__ It's the text that's gets generated after plaintext is undergone the encryption process.
* __Encryption & Decryption:__ Converting plaintext to ciphertext is called encryption and ciphertext to plaintext is called decryption.
* __Ciphers:__ Both encryption and decryption algorithm together is called ciphers.
* __Key:__ Ciphers(Encryption and Decryption algorithms) use a key to encrypt or decrypt the data.

## AES Algorithm:
```go
func main() {
	key := []byte("passphrasewhichneedstobe32bytes!") // 32 byte
	plainText := []byte("my super secret stuff")
	val, _ := Encrypt(plainText, key)
	fmt.Println(string(val))
	plainBytes, _ := Decrypt(val, key)
	fmt.Println(string(plainBytes))
}

// Encrypt will encrypt a raw string to an encrypted value
func Encrypt(plainText, key []byte) ([]byte, error) {
	// generate a new AES cypher using 32byte long key
	aesCypher, err := aes.NewCipher(key)
	if err != nil {
		fmt.Println(err)
		return nil, err
	}

	// gcm or Galois/Counter Mode, is a mode of operation
	// for symmetric key cryptographic block ciphers
	// - https://en.wikipedia.org/wiki/Galois/Counter_Mode
	gcm, err := cipher.NewGCM(aesCypher)
	if err != nil {
		return nil, err
	}

	// nonce is a random string to be used once. Default value of nonce is 12bytes
	nonce := make([]byte, gcm.NonceSize())
	// fill the nonce with random value
	if _, err := io.ReadFull(rand.Reader, nonce); err != nil {
		return nil, err
	}

	// encrypt plaintext to :- IV (nonce) + actual encrypted value
	// when we decrypt, we only decrypt the latter part
	return gcm.Seal(nonce, nonce, plainText, nil), nil
}

func Decrypt(cipherText, key []byte) ([]byte, error) {
	aesCypher, err := aes.NewCipher(key)
	if err != nil {
		return nil, err
	}

	gcm, err := cipher.NewGCM(aesCypher)
	if err != nil {
		return nil, err
	}

	nonceSize := gcm.NonceSize()
	if len(cipherText) < nonceSize {
		fmt.Println("invalid cipher text")
		return nil, err
	}
	nonce, encryptedPlainText := cipherText[:nonceSize], cipherText[nonceSize:]
	plainText, err := gcm.Open(nil, nonce, encryptedPlainText, nil)
	if err != nil {
		return nil, err
	}
	return plainText, nil
}
```