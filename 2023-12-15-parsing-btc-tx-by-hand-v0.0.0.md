# Parsing a Bitcoin Transaction By Hand

> *This tutorial follows the lessons taught by [*Base58 Bitcoin Developer Basics: Transactions, Scripts + SegWit*](https://www.udemy.com/course/base58-bitcoin-transactions-one/)


To parse a Bitcoin transaction, it is a pre-requisite to understand encoding, decoding, and endianess. Hence, this analysis will assume the knowledge of calculating a hex coded number back to integer, and a sequence of bytes organized from little endian to big endian. 

Here is a high-level illustration of the field types in a Bitcoin transaction.

![Field Types](https://github.com/hazelblitz/tech-writing-illustrations/blob/master/parsing-btc-tx-by-hand.png?raw=true)

## First Ever Bitcoin Transaction: Satoshi Nakamoto Sent Hall Finney 10 BTC

You can look up the transaction on blockstream.info using the txid below. 

txid = 'f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16'

If you are using Bitcoin Core, you can pass the txid as a parameter and fetch the raw_hex, or raw transaction data, using this command: 

```
bitcoin-cli getrawtransaction f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16

```
I added spaces in between fields of the raw_hex as form of pre-parsing, but below I explain step-by-step what each field means and how to decode them. 

raw_hex = '01000000 01 c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704 00000000 48 47304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901 ffffffff 02 00ca9a3b00000000 43 4104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac 00286bee00000000 43 410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac 00000000'

## Decoding 

### Version

The first field in the raw_hex is the version number that has a **fixed length** of 4 bytes. That means 8 hexadecimal characters. Fixed-length fields are always little endian. They must be converted to big endian, and then decoded from hex to integer. This Python code will do the conversion for you: 

```
# Python_1 script to decode fixed-length fields to an integer.

value = int.from_bytes(bytes.fromhex('hex_value_with_quotes'), 'little')
print(value)

```
**-->** *Indicates the process of decoding, or the conversion*. 

"version", 4 bytes, 8 hex characters: 01000000 --> 1

### Count of Inputs, input_count

The second field represents the count of inputs. It has 1-byte and its format is CompactSize.

CompactSize fields have some tricky rules. First, you'll always parse the first byte, 2 hexadecimal characters, and convert from hex to int, straight up. 

If the value is less than 253, you don't need to worry about endianess because the value is within a single byte. Just use the value you got from the direct conversion from hex to integer.

If the value is 253, the input count will be the next 2 bytes, 4 hexadecimal characters. Remember that at this point you MUST convert from little endian to big endian before converting from hex to integer. 

If the number is 254, the input count will be the next 4 bytes, 8 hexadecimal characters. Convert from little endian to big endian before converting hex to integer. 

If the number is 255, the input count will be the next 8 bytes, 16 hexadecimal characters. Convert from little endian to big endian before converting hex to integer. 

"input_count", 1 byte at first, 2 hex characters: 01 --> 1

### Source Transaction ID, txid

The next step is to parse the tx id source, or the id of the origin of the input. Here, you will keep the field in hexadecimal. Different from other fixed-length fields in a Bitcoin transaction, the tx ids should not be converted to integer and need to be kept in hex. 

However, you need to convert from little endian to big endian. This field is always 32-bytes, 64 hexadecimal characters. 

Since you will NOT decode from hex to int, this fixed length hex needs to be converted to bytes, then from little endian to big endian, then back to hex. 

You'll know you parsed correctly if you can use the decoded value in a block explorer and find the transaction there. 
See below a simple Python script that can do that for you. 

```
# Python_2 script to break down hex to bytes, reorganize from little endian to big endian, then assemble the value back to hex. 

txid_bytes = bytes.fromhex('hex_value_with_quotes')[::-1].hex()
print(txid_bytes)

```

"txid", 32 bytes, 64 hex characters: c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704  ---> 0437cd7f8525ceed2324359c2d0ba26006d92d856a9c20fa0241106ee5a597c9

### Vector Output or Output Index, vout

The next field is the vout, vector output or output index. It is the index of the output of the previous transaction that is being used as an input for the current transaction. 

Note that it can be fetched only after we have the source tx id (id of the previous transaction). The vout has a fixed length of 4 bytes. You need to convert the vout from little endian to big endian, then from hex to int. You can use the Python_1 script.

"vout", 4 bytes, 8 hex characters: 00000000 --> 0

### Unlocking Script Length, scriptSig_len

Now we have another CompactSize field representing the scriptSig length. Depending on the complexity of the spending rules, the scriptSig can have a variable number of hexadecimal characters. The scriptSig_len determines such number so we can parse accordingly.  

First let's convert the first byte from hex to int and see if it is smaller than 253.

"scriptSig_len", 1 byte at first, 2 hex characters: 48 --> 72 bytes --> 144 hex characters

Since 72 it is smaller than 253, let's count 144 hexadecimal characters. 

*Note that the CompactSize in scriptSig_len represents a length in bytes, while a CompactSize in input_count represents the pure counting of integers.*

### Unlocking Script, scriptSig

The next 144 characters are the unlocking script, or scriptSig. This value is already in big endian and does not require conversion. 

"scriptSig", variable size: 4847304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901

### nSequence

Next is the nSequence, a fixed-length field of 4-bytes, 8 hexadecimal characters. It is in little endian and can be converted to big endian, then from hex to int using that previous Python_1 script. 

The versatility of the nSequence field reflects the evolving nature of Bitcoin's protocol and its adaptability to new use cases and enhancements. It plays a crucial role in enabling various types of transaction flexibility, particularly in terms of timelocks and transaction replacement policies.

"sequence", 4 bytes, 8 hex characters: ffffffff --> 4294967295

### Count of Outputs, output_count

Theis field represents the count of outputs. You can follow the same rules of decoding used in the count of inputs. Decode the first byte and follow the rules of CompactSize decoding. 

"output_count", 1 byte at first, 2 hex characters: 02 --> 2 

### Value in Satoshis, amount

Now it is time to calculate the amount in satoshis of this tx. This value is a fixed-length with 8 bytes. You can use the Python_1 script. 

"amount", 8 bytes, 16 hex characters:  00ca9a3b00000000 --> 1000000000

### Locking Script Length, scriptPubKey_len

Now comes the script length of the output script, or locking script. Remember that it is a CompactSize that will give the length in bytes. Let's parse the first byte from hex to int and see if the number of bytes is smaller than 253. 

"scriptPubKey_len", 1-byte at first, 2 hex characters : 43 --> 67 bytes --> 134 hex characters

### Locking Script, scriptPubKey

The next 134 characters are the locking script, or scriptPubKey. This value is already in big endian and does not require conversion. 

"scriptPubKey", variable size: 4104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac 

### Second Output Value in Satoshis, amount

Next, we'll parse the second amount value to satoshis. You can use the Python_1 script. 

"amount", 8 bytes, 16 hex characters : 00286bee00000000 --> 4000000000

### Second Output Locking Script Length, scriptPubKey_len

Now let's parse the second locking script length, in CompactSize.

"scriptPubKey_len", 1 byte at first, 2 hex characters : 43 --> 67 bytes --> 134 hex characters

### Second Output Locking Script, scriptPubKey

Now, by counting the next 134 hex characters, we have the scriptPubkey proper. 

"scriptPubKey", variable size: 410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac

### nLocktime

The last value is the locktime. It is a fixed length field. Convert it from 4-byte little endian then from hex to an integer. You can use that Python_1 script here too. 

"locktime", 4 bytes, 8 hex characters: 00000000 --> 0

## Parsed-by-Hand tx in a Python dict format 

```
parsed_tx = {
    "version": 1,
    "input_count": 1,
    "inputs": [
        {
            "txid": '0437cd7f8525ceed2324359c2d0ba26006d92d856a9c20fa0241106ee5a597c9',
            "vout": 0,
            "scriptSig_len": 72,
        "scriptSig": '47304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901',
            "sequence": 4294967295,
        },
    ],
    "output_count": 2,
    "outputs": [
        {
            "amount": 1000000000,
            "scriptPubKey_len": 67,
            "scriptPubKey": '4104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac'
        },
        {
            "amount": 4000000000,
            "scriptPubKey_len": 67,
            "scriptPubKey": '410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac'
        }, 
    ],
    "locktime": 0
}
```
