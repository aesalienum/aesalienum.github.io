# BLOG

## 本地运行

docker run -it --rm \
--net host --volume="$PWD:/srv/jekyll" \
-p 4000:4000 myjekyll \
jekyll serve