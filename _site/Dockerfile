# this file exists because Docker Hub can't make images out of build stages
# so this is a bit redundant with Dockerfile in this repo
# but it lets us host two images, mostly the same, on Hub

FROM jekyll

EXPOSE 4000

COPY ./ /

WORKDIR /site

ENTRYPOINT [ "jekyll" ]

CMD [ "--help" ]

docker run --rm --volume="$PWD:/srv/jekyll" --volume="$PWD/vendor/bundle:/usr/local/bundle" --env JEKYLL_ENV=development -p 4000:4000 jekyll/jekyll:3.6 jekyll serve

docker run --name myblog --volume="$PWD:/srv/jekyll" -p 4000:4000 -it jekyll/jekyll:$JEKYLL_VERSION jekyll serve --watch --drafts
