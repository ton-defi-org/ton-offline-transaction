# Tails OS

## Compiling ton executables

If running into problems with these steps:

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

Now run the steps above.

Make sure to continue the rest of the non-sudo steps (starting with `git clone`) without `sudo` as the regular `amnesia` user.
