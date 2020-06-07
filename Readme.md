## Disassembler's build notes

**Note:** To just get the mobile editors working again, *web-apps* can be patched without full rebuild. Full build is needed in order to raise the default connection limit (25). See my notes in https://github.com/Disassembler0/oo-build_tools/.

```
# Prepare environment
lxc init ubuntu:20.04 onlyoffice
lxc start onlyoffice
lxc exec onlyoffice -- bash

# Install tools
curl -sL https://deb.nodesource.com/setup_12.x -o - | bash
apt-get -y install --no-install-recommends git nodejs
npm install -g grunt-cli

# Clone repo and run build script
git clone https://github.com/Disassembler0/oo-web-apps.git web-apps
cd web-apps/build
npm install
grunt --force

# Cherry-pick and archive the mobile apps
cd ../deploy
tar czf web-apps.patched.tgz web-apps/apps/*/mobile/app.js

# Unpack the archive on the target instance
tar xzf web-apps.patched.tgz -C /var/www/onlyoffice/documentserver
chmod ds:ds /var/www/onlyoffice/documentserver/web-apps/apps/*/mobile/app.js
```

The files can be injected also to the official deb package using following steps.

```
# Download and unpack
wget http://download.onlyoffice.com/repo/debian/pool/main/o/onlyoffice-documentserver/onlyoffice-documentserver_amd64.deb
dpkg -x onlyoffice-documentserver_amd64.deb onlyoffice-documentserver
dpkg -e onlyoffice-documentserver_amd64.deb onlyoffice-documentserver/DEBIAN

# Patch
tar -xzf web-apps.patched.tgz -C onlyoffice-documentserver/var/www/onlyoffice/documentserver
cd onlyoffice-documentserver
for FILE in var/www/onlyoffice/documentserver/web-apps/apps/*/mobile/app.js; do sed -i "s|.*${FILE}|$(md5sum ${FILE})|" DEBIAN/md5sums; done

# Repack
cd ..
dpkg -b onlyoffice-documentserver onlyoffice-documentserver_5.5.3-39_amd64.deb
```

[![License](https://img.shields.io/badge/License-GNU%20AGPL%20V3-green.svg?style=flat)](https://www.gnu.org/licenses/agpl-3.0.en.html)

## web-apps

The frontend for [ONLYOFFICE Document Server][2]. Builds the program interface and allows the user create, edit, save and export text, spreadsheet and presentation documents using the common interface of a document editor.

## Previous versions

Until 2019-10-23 the repository was called web-apps-pro

## Project Information

Official website: [http://www.onlyoffice.org](http://onlyoffice.org "http://www.onlyoffice.org")

Code repository: [https://github.com/ONLYOFFICE/web-apps](https://github.com/ONLYOFFICE/web-apps "https://github.com/ONLYOFFICE/web-apps")

SaaS version: [http://www.onlyoffice.com](http://www.onlyoffice.com "http://www.onlyoffice.com")

## User Feedback and Support

If you have any problems with or questions about [ONLYOFFICE Document Server][2], please visit our official forum to find answers to your questions: [dev.onlyoffice.org][1] or you can ask and answer ONLYOFFICE development questions on [Stack Overflow][3].

  [1]: http://dev.onlyoffice.org
  [2]: https://github.com/ONLYOFFICE/DocumentServer
  [3]: http://stackoverflow.com/questions/tagged/onlyoffice

## License

web-apps is released under an GNU AGPL v3.0 license. See the LICENSE file for more information.
