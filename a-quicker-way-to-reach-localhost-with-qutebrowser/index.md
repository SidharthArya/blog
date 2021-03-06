# A quicker way to reach localhost with qutebrowser


## Qutebrowser {#qutebrowser}

[Qutebrowser](https://qutebrowser.org/) is the best web browser for developers, hands down. Nothing can compare to the flexibility it offers. Now, ofcourse other web browser must have ways of enabling and disabling various fancy features, but it's much harder to achieve.

I have a number of scripts that i use on a daily basis in qutebrowser which i maintain here ([SidharthArya/.qutebrowser](https://github.com/SidharthArya/.qutebrowser)) on a daily basis.


## The Problem {#the-problem}

One of the issues i have sometimes is to reach localhost on various ports quickly. For example, i may have a hugo server running on port 1313 accessed as <https://localhost:1313> and i may have an [org-roam-server](https://github.com/org-roam/org-roam-server) running on port 8080 accessed as <https://localhost:8080>.

Using the regular history matching of a web browser may work, nonetheless, some developing environments have dynamic allocation of ports. So it may associate a port number at random with its services. In this specific case history matching does not work for me. Also history matching feels a lot slower in every browser compared to what we can do in qutebrowser.

So, what i do is i write a simple [qutebrowser userscript](https://qutebrowser.org/doc/userscripts.html) as follows:


## Solution {#solution}

1.  Create a file called localhost at ~/.local/share/qutebrowser/userscripts (or ~/.config/qutebrowser/userscripts)
2.  Write the code below in that file:

    ```bash
    #!/bin/bash
    echo open localhost:${QUTE_COUNT:-8000} > $QUTE_FIFO
    ```
3.  Save the file and change its permission to executable by runnign ~chmod +x ~/.local/share/qutebrowser/userscripts/localhost
4.  add a key combination for the script in your `config.py` usually placed at ~/.config/qutebrowser/config.py.

    ```python
    config.bind('zl', 'spawn --userscript localhost')
    ```


## Usage {#usage}

If you need to go to <https://localhost:8080>. Type 8080zl in normal mode in qutebrowser.
8000 is the default port and so just typing zl would take you to <https://localhost:8000>.


## Misc {#misc}

You can instead choose to goto the last port used by writing to a file, as shown below

```bash
#!/bin/bash
if [ -z $QUTE_COUNT ];
then
    QUTE_COUNT=$(cat /tmp/qutebrowser-localhost-port)
fi
echo open localhost:$QUTE_COUNT > $QUTE_FIFO
echo $QUTE_COUNT >  /tmp/qutebrowser-localhost-port
```


## List open ports using dmenu {#list-open-ports-using-dmenu}

Okay, the credit for this belongs here: [An improved localhost userscript : qutebrowser](https://www.reddit.com/r/qutebrowser/comments/nua8ks/an%5Fimproved%5Flocalhost%5Fuserscript/)

```bash
#!/bin/bash

if [[ $1 -eq 'list' ]] && [[ -z $QUTE_COUNT ]];
then
    PORTS="$(ss -nltp | tail -n +2 |  awk '{print $4}' | awk -F: '{print $2}')"
    QUTE_COUNT=$(echo "$PORTS" | dmenu )
fi

echo open -t localhost:${QUTE_COUNT:-8080} > $QUTE_FIFO
```

Bind this instead, if you want to use the list instead of a default port.

```python
config.bind('zl', 'spawn --userscript localhost list')
```


## References {#references}

-   [qutebrowser | qutebrowser](https://qutebrowser.org/)
-   [Writing qutebrowser userscripts | qutebrowser](https://qutebrowser.org/doc/userscripts.html)
