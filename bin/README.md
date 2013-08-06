# How do I use some of these scripts?

----
## clusterssh helper scripts

I wrote a few scripts which help me maintain my `/etc/clusters` file.  I maintain my `/etc/clusters` file with a standard naming convention.  The first line has an `All_clusters` alias.  Its only purpose is to be an alias for all aliases in the `/etc/clusters` file.  From there every alias starts with one of two standard prefixes: `cluster_` or `host_`.

Here is a sample `/etc/clusters` file using that naming convention.

    All_clusters cluster_website cluster_dns host_Config_management
    
    cluster_website host1.domain.com host2.domain.com host3.domain.com
    
    cluster_dns ns1.domain.com ns2.domain.com
    
    host_Config_management someconfigmanagement.domain.com

I use the following helper scripts to maintain the `/etc/clusters` file:

* `knownhosts.sh`
* `missing_from_all_clusters.sh`
* `servercount`
* `sort_clusters`

`knownhosts.sh` - This script reads stdin a list of host names, queries the ssh fingerprint, and checks to see if that known host exists in `~/.ssh/known_hosts`.  If it exists then it outputs nothing.  If there's any missing (or possibly incorrect) then it will output only the problem hosts.  If no hosts have any problems then it exits with a proper success exit code.  This can be used with `servercount`.

`missing_from_all_clusters.sh` - This goes through the `/etc/clusters` file for all of the aliases and checks to make sure that all aliases are added to `All_clusters`.  If there is no alias then it will output the problem entry.  There will be no output if all entries are properly accounted for.

`servercount` - This goes through the `/etc/clusters` file and displays a list of host names only (with no aliases).  This will consist of one host per line.

`sort_clusters` - As you keep adding aliases to `/etc/clusters` there becomes a need to alphabetically sort the aliases in the file.  This will sort the aliases.  It also sorts the list of aliases on the `All_clusters` line at the top of the file.

### Example usage

Get a head count of the number of servers in the clusters file.

    servercount | wc -l

Check that there aren't any bad `known_hosts` fingerprints for clusters host names.

    servercount | knownhosts.sh

The remaining scripts are fairly standalone.

----
## wasted-ram-updates.py

Ever hear about Linux being able to update without ever need to be restarted (with exception for a few critical packages)?  Ever wonder how to figure out which services need to actually be restarted after a large update of hundreds of packages?  With `wasted-ram-updates.py` you no longer need to wonder.

`wasted-ram-updates.py` helps to resolve these questions by showing which running processes are using files in memory that have been deleted on disk.  This lets you know that there is likely an outdated library being used.  If you restart the daemon associated with this process then it will use the updated copy of the library.

### Example usage

Just display an overall summary

    wasted-ram-updates.py summary

Organize the output by deleted file handle (I've found this to be less useful for accomplishing a system update).

    wasted-ram-updates.py

Organize the output by process ID and show a heirarchy of deleted file handles as children to the PIDs.  This is the most useful command for determining which services to restart.

    wasted-ram-updates.py pids
