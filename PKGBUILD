# Maintainer: Fixed Torres <aur_linuxero@outlook.com>
# Contributor: Fixed Torres <aur_linuxero@outlook.com>

pkgname=dnscrypt-proxy
pkgver=1.9.4
pkgrel=1
pkgdesc="Is a protocol for securing communications between a client and a DNS resolver."
arch=('i686' 'x86_64')
url="http://dnscrypt.org/"
license=('custom:ISC')
depends=('libsodium' 'systemd' 'libtool' 'ldns')
options=(libtool)
backup=('etc/dnscrypt-proxy.conf')
install=dnscrypt-proxy.install
source=("https://download.dnscrypt.org/dnscrypt-proxy/${pkgname}-${pkgver}.tar.gz")

prepare() {
 cd ${srcdir}/${_pkgname}
  sed -e 's|^ExecStart=.*|ExecStart=/usr/bin/dnscrypt-proxy /etc/dnscrypt-proxy.conf|' \
         -i dnscrypt-proxy.service.in
  sed -e 's|python|python2|' -i contrib/generate-domains-blacklist.py
  sed -e 's|^PKG_DATA_DIR=.*|PKG_DATA_DIR="../.."|' \
         -i contrib/dnscrypt-update-resolvers.sh.in
}

build() {
  cd ${srcdir}/${pkgname}-${pkgver}
	./autogen.sh --prefix=/usr --sbindir=/usr/bin --sysconfdir=/etc --with-systemd
	./configure --prefix=/usr --sbindir=/usr/bin --sysconfdir=/etc --with-systemd
	make -j2
}

package() {
  cd ${srcdir}/${pkgname}-${pkgver}
	make DESTDIR="$pkgdir" install

	install -dm755 "$pkgdir"/{usr/share/{licenses,doc}/$pkgname,usr/lib/systemd/system,usr/lib/${pkgname}}
	install -dm755 "$pkgdir/usr/share/${pkgname}/script"
	install -m 644 COPYING "$pkgdir"/usr/share/licenses/${pkgname}
	install -m 644 AUTHORS NEWS README README.markdown "$pkgdir"/usr/share/doc/${pkgname}
	install -m 644 dnscrypt-proxy.service "$pkgdir"/usr/lib/systemd/system
    install -m 644 dnscrypt-proxy.service.in "$pkgdir"/usr/lib/systemd/system/dnscrypt-proxy.service
	install -m 644 dnscrypt-proxy.socket "$pkgdir"/usr/lib/systemd/system

## Installing script to generate domains blacklist
	cd ${srcdir}/${pkgname}-${pkgver}/contrib/
	install -m 644 generate-domains-blacklist.py "$pkgdir/usr/share/${pkgname}/script"
	install -m 644 domains-blacklist.conf "$pkgdir/usr/share/${pkgname}/script"
	install -m 644 *.sh.in "$pkgdir/usr/share/${pkgname}/script/dnscrypt-update-resolvers.sh"
	install -m 644 *.txt "$pkgdir/usr/share/${pkgname}/script"

cat > "$pkgdir/usr/bin/dnscrypt-update-resolvers" << EOF
#!/bin/bash
cd /usr/share/dnscrypt-proxy/script && sh dnscrypt-update-resolvers.sh "\$@"
EOF

cat > "$pkgdir/usr/bin/dnscrypt-domains-blacklist" << EOF
#!/bin/bash
cd /usr/share/dnscrypt-proxy/script && python2 generate-domains-blacklist.py "\$@"
EOF

	chmod 755 "${pkgdir}/usr/bin/dnscrypt-update-resolvers"
	chmod 755 "${pkgdir}/usr/bin/generate-domains-blacklist"
}
sha512sums=('b19103dc39655f199c85ec69daab949d07adc12e64411d2323308afb398e6ac6563bbc17f2e317bfe1e6c95c810487d8d62da3f4c7f7257bdcf837cd0390bfb0')
