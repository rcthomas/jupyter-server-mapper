# jupyter-server-mapper

This is a server extension that builds on jupyter-server-proxy.
It allows you to proxy to a host based on a key.
That key could be anything you want.

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


