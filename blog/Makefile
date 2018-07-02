push:
	git push
	ssh nginx 'cd /srv/blog && git checkout . && git pull && make style && hugo'

export PATH := $(PATH):/usr/local/nodejs/bin

style:
	node-sass --output static/static/css static/static/css/style.scss
	node-sass --output static/static/css static/static/css/spf13-1.scss
