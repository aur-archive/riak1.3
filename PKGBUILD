# Maintainer: lafka <lafa@hackeriet.no>
# Contributor: Albert Chang <albert.chang@gmx.com>
# Contributor: Thomas Mudrunka <harvie@@email..cz> You can also contact me on http://blog.harvie.cz/
# Contributor: zhehao
# Contributor: Tristan Rice <rice@outerearth.net>

pkgname=riak1.3
pkgver_maj=1.3
pkgver=1.3.1
pkgrel=1
pkgdesc='NOSQL database engine providing decentralized key-value store, flexible map/reduce engine and HTTP/JSON query interface. Requires Erlang R15B01 (package downgrade needed).'
arch=('i686' 'x86_64')
license=('APACHE')
url='http://riak.basho.com/'
conflicts=('riak')
provides=('riak')
makedepends=('erlang')
backup=('opt/riak/etc/app.config' 'opt/riak/etc/vm.args')
install='riak.install'
source=("http://s3.amazonaws.com/downloads.basho.com/riak/$pkgver_maj/$pkgver/riak-$pkgver.tar.gz"
        'riak'
        'riak-admin'
        'riak.rc'
        'riak.service')
md5sums=('dec210872c5465dde532f5157194f2b1'
         'ea204ebd9a06a3812a00f4dcbd37c227'
         'af9a1757a5011d23712555adc98c133f'
         'f48643d5186dbbd1c2cd65f7bde2511c'
         'ed4cda47fc1fad8b0a3fb3858600c578')

build() {
  cd ${srcdir}/riak-${pkgver}

  msg 'Cleaning...'
  make distclean

  msg 'Building...'
  # need to unset LDFLAGS because: ld: unrecognized option '-Wl,--hash-style=gnu'
  unset LDFLAGS
  make rel 
}

package() {
  install -d ${pkgdir}/opt/riak

  cd ${srcdir}/riak-${pkgver}/rel/riak

  cp -r ./* ${pkgdir}/opt/riak

  [ -d "${pkgdir}/usr/lib/erlang" ] && {
    msg 'Removing files already shipped with erlang package...'
    pacman -Ql erlang | cut -d ' ' -f 2- | while read i; do
      [ -d "$i" ] || {
        rm -f ${pkgdir}/"$i";
        echo -ne "$i\r";
      }
    done; echo;
  }

  chmod -R 755 ${pkgdir}/opt/riak/bin

  msg 'Archlinux Integration'

  install -d ${pkgdir}/var/log
  ln -s /opt/riak/log ${pkgdir}/var/log/riak
  install -d ${pkgdir}/etc/rc.d
  install -d ${pkgdir}/usr/lib/systemd/system
  ln -s /opt/riak/etc ${pkgdir}/etc/riak
  install -d ${pkgdir}/usr/share/doc/
  cp -r ${srcdir}/riak-${pkgver}/doc/man ${pkgdir}/usr/share/
  cp -r ${srcdir}/riak-${pkgver}/doc ${pkgdir}/usr/share/doc/riak
  rm -r ${pkgdir}/usr/share/doc/riak/man

  # install scripts
  install -d ${pkgdir}/usr/bin
  install ${srcdir}/riak ${pkgdir}/usr/bin/riak
  install ${srcdir}/riak-admin ${pkgdir}/usr/bin/riak-admin

  # install daemon
  install ${srcdir}/riak.rc ${pkgdir}/etc/rc.d/riak
  install ${srcdir}/riak.service ${pkgdir}/usr/lib/systemd/system/riak.service

  chmod -R 755 ${pkgdir}/usr/share/*
}
