# Tails OS

## Compiling ton executables

If running into problems with running:

```
sudo apt update
sudo apt install make cmake g++ libssl-dev zlib1g-dev
```

Make sure you have root enabled on the machine, clock that is set to current date and a working TOR connection.

Open `System Tools` > `Synaptic Package Manager` >> `Settings` > `Repositories` and add:

```
tor+http://deb.debian.org/debian/ buster main
tor+http://security.debian.org/debian-security/ buster/updates main
tor+http://deb.debian.org/debian/ buster-updates main
```
