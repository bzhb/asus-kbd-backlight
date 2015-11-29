# Author:  Stefan Majewsky <majewsky at-the-server gmx with-the-tld net>
# Author:  2015 Renan Fargetton

pkgname=asus-screen-backlight
pkgver=1.0
pkgrel=1
pkgdesc="Helper for adjusting screen backlight brightness in Asus Zenbook UX31A and similar models"
url="https://wiki.archlinux.org/index.php/ASUS_Zenbook_Prime_UX31A"
arch=('any')
license=(FDL1.3)
depends=('bash')
optdepends=('systemd: automatically grant user access to keyboard backlight control')
install="asus-screen-backlight.install"

# general note: this package contains only two files; I don't see any added
# value in stuffing these into a tarball when I can also place them in here
# using heredocs (cf. http://stackoverflow.com/a/2954835/334761 for reference)
build() {
	mkdir -p $srcdir/$pkgname-$pkgver/
	cd $srcdir/$pkgname-$pkgver/
	# write the backlight helper script
	cat <<-'EOF' > ./asus-screen-backlight
		#!/bin/bash
		# Author : Renan Fargetton 2012
		# Licence : GNU GPL v3 or later
		
		path="/sys/class/backlight/intel_backlight"
		# alternative interface (0 < x < 10)
		#path="/sys/class/backlight/acpi_video0/"
		
		# max should be 4296
		max=$(cat ${path}/max_brightness)
		# step: represent the difference between previous and next brightness
		step=$(($max/10))
		# for a value smaller than treshold, the steps are smaller for a more accurate setting at low intensity
		treshold=$((2*$step))
		previous=$(cat ${path}/brightness)
		
		function commit {
			if [[ $1 = [0-9]* ]]
			then 
				if [[ $1 -gt $max ]]
				then 
					next=$max
				elif [[ $1 -lt 0 ]]
				then 
					next=0
				else 
					next=$1
				fi
				echo $next >> ${path}/brightness
				exit 0
			else 
				exit 1
			fi
		}
		
		case "$1" in
		 up)
			if [[ $previous -le $treshold ]]
				then 
					step=$(($previous/4))
					if [[ $previous -le 4 ]] ; then step=1 ; fi
			fi
			 commit $(($previous + $step))
		   ;;
		 down)
			if [[ $previous -le $treshold ]]
				then 
					step=$(($previous/4));
					if [[ $previous -le 4 ]] ; then step=1 ; fi
			fi
			commit $(($previous - $step))
		   ;;
		 max)
			 commit $max
		   ;;
		 off)
			 commit 0
		   ;;
		 show)
			 echo $previous
		   ;;
		 night)
			 commit $(($max/4)) 
		 	 ;;
		 allowusers)
			 # Allow members of users group to change brightness
			 sudo /usr/bin/chgrp users ${path}/brightness
			 sudo /usr/bin/chmod g+w ${path}/brightness
		   ;;
		 u)
			 $0 allowusers
		   ;;
		 disallowusers)
			 # Allow members of users group to change brightness
			 sudo /usr/bin/chgrp root ${path}/brightness
			 sudo /usr/bin/chmod g-w ${path}/brightness
		   ;;
		 *)
			 commit	$1
		esac
		
		exit 0
	EOF
	# write the systemd unit file
	cat <<-'EOF' > ./asus-screen-backlight.service
		[Unit]
		Description=Allow user access to screen backlight
		After=systemd-udevd.service
		
		[Service]
		ExecStart=/usr/bin/asus-screen-backlight allowusers
		
		[Install]
		WantedBy=multi-user.target
	EOF
}

package() {
	cd $srcdir/$pkgname-$pkgver/
	install -D -m 0755 asus-screen-backlight "$pkgdir/usr/bin/asus-screen-backlight"
	install -D -m 0644 asus-screen-backlight.service "$pkgdir/usr/lib/systemd/system/asus-screen-backlight.service"
}

