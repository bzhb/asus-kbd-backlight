# Author:  Stefan Majewsky <majewsky at-the-server gmx with-the-tld net>
# Author:  2012-2022 Renan Fargetton

pkgname=deep-throttle-screen-backlight
pkgver=1.1
pkgrel=1
pkgdesc="Helper for adjusting screen backlight brightness with deep throttle on the dim side"
url="https://github.com/bzhb/deep-throttle-screen-backlight"
arch=('any')
license=(GPLv3+)
depends=('bash' 'sudo' 'coreutils')
optdepends=('systemd: automatically grant user access to keyboard backlight control')
install="deep-throttle-screen-backlight.install"

# general note: this package contains only two files; I don't see any added
# value in stuffing these into a tarball when I can also place them in here
# using heredocs (cf. http://stackoverflow.com/a/2954835/334761 for reference)
build() {
	mkdir -p $srcdir/$pkgname-$pkgver/
	cd $srcdir/$pkgname-$pkgver/
	# write the backlight helper script
    cp ../../deep-throttle-screen-backlight .
	# write the systemd unit file
	cat <<-'EOF' > ./deep-throttle-screen-backlight.service
		[Unit]
		Description=Allow user access to screen backlight
		After=systemd-udevd.service
		
		[Service]
		ExecStart=/usr/bin/deep-throttle-screen-backlight allowusers
		
		[Install]
		WantedBy=multi-user.target
	EOF
}

package() {
	cd $srcdir/$pkgname-$pkgver/
	install -D -m 0755 deep-throttle-screen-backlight "$pkgdir/usr/bin/deep-throttle-screen-backlight"
	install -D -m 0644 deep-throttle-screen-backlight.service "$pkgdir/usr/lib/systemd/system/deep-throttle-screen-backlight.service"
}

