## IDE 50

### Rolling a new version of ide50 deb 

Assumes your cwd is the root of this repo.

1. Ensure that you've updated the [plugins](https://github.com/cs50/ide50-plugin) and the [CS50 workspace config file](https://github.com/cs50/ide50-docker/blob/master/ide50-offline/files/client-workspace-cs50.js) before proceeding, as building the deb pulls the latest commit from Master.
1. Make desired changes to files within `ide50/files`
1. Be sure to ovewrite `ide50/files/etc/version50` with the new version.
1. Use your favorite text editor to add a new changelog entry to `ide50/debian/changelog`
1. If you'd like to chmod stuff, do so in `ide50/debian/postinst`
1. Now make the local deb with `make deb`
1. New deb can be found at `build/deb/`! At this point if you'd like to test the deb in your machine before pushing to mirror you can do `sudo -E dpkg --force-confnew --force-confmiss -i ide50_*.deb`

### Pushing deb to mirror
1. Build new version of deb as described above.
1. `scp ide50_*.deb user@local.cdn.cs50.net:~ # copy the deb to mirror`
1. `ssh user@local.cdn.cs50.net`
1. `sudo su rbowden # rbowden has the script to update the deb in mirror`
1. `cp ide50_*.deb /home/rbowden # copy the deb to rbowden's home folder`
1. `cd # go to rbowden's home`
1. `chown rbowden:producers ide50_*.deb # fix ownership`
1. `sudo bash #prep environment`
1. Finally execute the scrip  that effectively uploads all debs in rbowden's home folder to production. It will ask for gpg password which is in the Heads 1password.
1. `sh deb/deb64.sh`
1. If you want to delete older versions: `cd /srv/www/vhosts/mirror.cs50.net/htdocs/ide50/2015/dists/trusty/main/binary-amd64; rm ide50_{N-1}; push50 -d`
1. Wait a few moments for the mirrors to sync.
1. At this point it's a good idea to test `sudo apt-get update` in the IDE to make sure everything works. Also, logs and errors generated by update50 can be found at `/home/ubuntu/lib/ide50.log`

