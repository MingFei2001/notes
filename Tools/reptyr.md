# Reptyr

If you disconnect from your ssh session, try this to reconnect back:

First calm down and install reptyr:
```bash
sudo apt install reptyr
```

then try to get the process id of said session:
```bash
ps -u $USER | grep <program name>

```

Grab the PID and use it with `reptyr`:

```bash
# this is what we called in the industry
# AssSaver3000 or tty-stealing mode
sudo reptyr -T <PID>
```

If you still cant connect back, then **Good Luck** i guess!
