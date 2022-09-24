# Single Point Tunneling

### Forward Tunnel

Invoke an additional SSH session, using the syntax below. Replace the 8111 with the port your python web server is listening on.

```
ssh -N -L 0.0.0.0:9111:10.0.17.200:8111 user.name@cyber@10.0.17.200
```

<figure><img src="https://lh6.googleusercontent.com/IsEqohrhGXgAwoSjyyImR-St6eG2n33KttiAZDzb9sBfvkHS5y5staOHuG9oY8mcngYTggrZ7L1MZSTvjzVwzF0gKB3mNoIsaTvz9vFdsUsl4v0j7ewyzASOih1qzHfy5O936F53hOONrG61x4YqxU7yZsD_UiKRtcV1i9HO_euF-CK4GA5K_KrD3Q" alt=""><figcaption><p>Example</p></figcaption></figure>

\- N means don't execute a remote command

\- L binds kali local address 0.0.0.0 port 9111 to remote address 10.0.17.200 port 8111 (change this to yours)

### Reverse Tunnel

When you don't have direct ssh access to the target but rather you have an interactive console like a reverse shell that you acquired during the process of landing a foothold on the target.

<figure><img src="https://lh5.googleusercontent.com/Dm8lR3sdBrqRZc_qg7wrBgqJ-4XcIQQsy3_gU6Iwo06neBwn7hwuDePQ5dcSuxvgCiaQVWeIR_GOOvFkqEa3NpE-iBGCWDqTivmfYUoUCxdJkUDfyzY3rAIN9eZ3pBfqRVussDfSRiUMM9oKYxqFl3lSMrPSGUbvth7Jex2ObbDVmaPHIBGRD3OxOA" alt=""><figcaption><p>Reverse Connection</p></figcaption></figure>

### Creating a Limited User



```bash
//adding user
useradd -m -s /usr/bin/bash throwaway
//creating key-pair on target
mkdir throwaway
cd throwaway
ssh-keygen -N "" -C throwaway -f throwaway
//sudo into throwaway on kali
su - throwaway
cd .ssh
nano authorized_keys
cat authorized_keys
pwd
chmod 700 .
chmod 600 authorized keys
ssh -o StrictHostKeyChecking=no -T -R 7000:localhost:8111 throwaway@10.0.17.50 -i throwaway
```

