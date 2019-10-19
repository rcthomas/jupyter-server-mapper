# jupyter-server-mapper

This is a server extension that builds on jupyter-server-proxy.
It allows you to proxy to a host based on a key.
That key could be anything you want.

## Explanation

Suppose you want users to be able to use `jupyter-server-proxy`-style access to the head node of a batch job allocation.
But it's a pain for them to look up the IP address or node ID based on a job ID.
Don't make them do `sbatch -h -o %B -j <job-id>` just do it for them.
In `jupyter_notebook_config.py` put

    import subprocess
    def mapper(key):
        return subprocess.run(["sbatch", "-h", "-o", "%B", "-j", key]).stdout
    c.ServerMapper.mapper = mapper

Then all they have to do is point their browser at

    https://jupyter.example.com/user/<user>/mapper/<job-id>:<port>/...

That's a bit more convenient.

## Installation

The mapper is built on top of jupyter-server-proxy.
It's very experimental so you probably shouldn't use it yourself if you happened on this page randomly.
If you're interested contact me directly.
Anyway, first you do:

    pip install jupyter-server-proxy

You don't need to enable the `jupyter-server-proxy` server extension unless you want to use it.
Then, install this repo and enable the extension.

    pip install git+https://github.com/rcthomas/jupyter-server-mapper.git
    jupyter serverextension enable --py jupyter_server_mapper --sys-prefix

Then you'll want to set up a mapper function in the config.
Otherwise it's just kind of like `jupyter-server-proxy` with a remote host all over again.
Also, if you're trying to enable this for all your users, use system-wide or prefix-wide configuration.
For instance, you could add the following:

    import subprocess
    def mapper(key):
        return subprocess.run(["sbatch", "-h", "-o", "%B", "-j", key]).stdout
    c.ServerMapper.mapper = mapper

to the prefix-wide configuration at

    <prefix>/etc/jupyter/jupyter_notebook_config.py

Then you are all done!
