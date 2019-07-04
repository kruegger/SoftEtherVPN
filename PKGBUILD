# Maintainer: Tun Win Naing <twnaing(at)outlook(dot)com>
# Maintainer: Jonathan Liu <net147@gmail.com>
pkgname=softethervpn
pkgver=v4.29_9680
pkgrel=1
pkgdesc="Multi-protocol VPN Program from University of Tsukuba"
arch=('i686' 'x86_64' 'aarch64' 'armv7h')
url="https://www.softether.org/"
license=('Apache')
depends=('bash' 'openssl' 'zlib')
source=("https://github.com/SoftEtherVPN/SoftEtherVPN_Stable/releases/download/${pkgver//_/-}-rtm/softether-src-${pkgver//_/-}-rtm.tar.gz"
        'aarch64.patch'
        'softethervpn-bridge.service'
        'softethervpn-client.service'
        'softethervpn-server.service')
md5sums=('0b0bf86c59374f5ea1dbde20b3a3b5d3'
         '8b92f69f9d8d852a3739d0e48bad0454'
         '51ae763eb186dff302ec15455faf026d'
         'c2600602409e933569391d179b122eae'
         'adfe4bc73cf1591a0daaf7b39a14b0b3')

build(){
  cd "${srcdir}/${pkgver//_/-}"
  patch -Np1 -i "${srcdir}/aarch64.patch"

  if [ "${CARCH}" == "i686" ]; then
    cp src/makefiles/linux_32bit.mak Makefile
  elif [ "${CARCH}" == "x86_64" ]; then
    cp src/makefiles/linux_64bit.mak Makefile
  elif [ "${CARCH}" == "aarch64" ]; then
    cp src/makefiles/linux_64bit.mak Makefile
  elif [ "${CARCH}" == "armv7h" ]; then
    cp src/makefiles/linux_32bit.mak Makefile
  fi

  make
}

package(){
  cd "${srcdir}/${pkgver//_/-}"

  install -Dm444 bin/vpnserver/hamcore.se2 "${pkgdir}"/opt/softethervpn/hamcore.se2
  install -d "${pkgdir}"/usr/bin

  for inst in vpnserver vpnclient vpncmd; do
    install -Dm755 bin/${inst}/${inst} "${pkgdir}"/opt/softethervpn/${inst}/${inst}
    ln -s /opt/softethervpn/hamcore.se2 "${pkgdir}"/opt/softethervpn/${inst}/hamcore.se2
    echo "#!/bin/sh" > "${pkgdir}"/usr/bin/${inst}
    echo /opt/softethervpn/${inst}/${inst} '"$@"' >> "${pkgdir}"/usr/bin/${inst}
    echo 'exit $?' >> "${pkgdir}"/usr/bin/${inst}
    chmod 755 "${pkgdir}"/usr/bin/${inst}
  done

  install -d "${pkgdir}"/usr/lib/systemd/system
  install -Dm644 "${srcdir}"/softethervpn-server.service "${pkgdir}"/usr/lib/systemd/system
  install -Dm644 "${srcdir}"/softethervpn-client.service "${pkgdir}"/usr/lib/systemd/system
}

# vim:set ts=2 sw=2 et:
