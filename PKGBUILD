# Maintainer: Fixed Torres <aur_linuxero@outlook.com>
# Contributor: Fixed Torres <aur_linuxero@outlook.com>

pkgname=dnscrypt-proxy
pkgver=1.9.2
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
  cd ${srcdir}/${pkgname}-${pkgver}

	sed -e 's|^ExecStart=.*|ExecStart=/usr/bin/dnscrypt-proxy /etc/dnscrypt-proxy.conf|' \
			-i dnscrypt-proxy.service
	
	sed -e 's|python|python2|' -i contrib/generate-domains-blacklist.py
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
	install -m 644 dnscrypt-proxy.socket "$pkgdir"/usr/lib/systemd/system

## Installing script to generate domains blacklist
	cd ${srcdir}/${pkgname}-${pkgver}/contrib/
	install -m 644 generate-domains-blacklist.py "$pkgdir/usr/share/${pkgname}/script"
	install -m 644 domains-blacklist.conf "$pkgdir/usr/share/${pkgname}/script"
	install -m 644 *.txt "$pkgdir/usr/share/${pkgname}/script"

cat > "$pkgdir/usr/bin/generate-domains-blacklist" << EOF
#!/bin/bash
cd /usr/share/dnscrypt-proxy/script && python2 generate-domains-blacklist.py "\$@"
EOF
	chmod 755 "${pkgdir}/usr/bin/generate-domains-blacklist"
}
sha512sums=('fc1ae2194622eea51885117fbdbd3094c35beaf70e1c7e918f90af6ca5b73fe3b0cd69ddb91e659d50fefc6fafc9d0a774762b50fde886b5447fe54a874db66e')