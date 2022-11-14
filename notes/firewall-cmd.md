# firewall-cmd
`firewall-cmd` is the CLI interface for interacting with [firewalld]({% link notes/firewalld.md%}).
By default, the client only makes changes to the current/runtime configuration of the firewall, *unless* the `--permanent` flag is used. 