# FAQ

## The `masternode outputs` return an empty object.
```
{
}
```

You must receive the **2000 XAX** for the **masternode** in a single transaction on your **Control Wallet** and wait for at least 1 confirmation.
> If you already have the **2000 XAX** but it came from multiple transactions, you can send the **2000 XAX** back to yourself:
```
docker exec artax-wallet artax-cli sendfrom "" YoUrAdDr3Ss XAX
```

## Running a **wallet** or a **masternode** container result in the following error:
```
No such file or directory
```

Ensure the `data` folder is *writable* for the container.
