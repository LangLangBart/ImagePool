
### 🔴 Problem
- GitHub does not provide a way to upload an image through its API. To work around this limitation, this repository hosts all the images I use in Issues and PullRequests.
  - More details on this limitation: [repo:cli/cli #1895](https://github.com/cli/cli/issues/1895#issuecomment-718899617)
### 👍 Solution request to GitHub
- There is already a [rate-limit](https://docs.github.com/en/rest/rate-limit) for using the GitHub API, a good solution would be to extend this for uploading images to limit abuse.
  - For example, 1 MB per file size and 30 MB per month (free offer).


### zsh command
```zsh
# Upload an image to GitHub and get a RAW URL
gup() {
	# TODO allow for multiple paths
	IMAGE_PATH="$(ruby --disable-gems -e 'puts File.expand_path(ARGV.first)' ${1:?Feed me one Path})"
	cd "$HOME"/Developer/ImagePool || return 1
	FILENAME="$(date +"%d_%b_%y_at_%H_%M_%S")_${IMAGE_PATH##*/}"
	cp "$IMAGE_PATH" "$HOME"/Developer/ImagePool/storage/"$FILENAME"
	git add "$(git ls-files -m -o --exclude-standard | tail -1)"
	git commit --message "File $(date +"%d/%b/%y %H:%M:%S")" || return 1
	git push --quiet || return 1
	# Build the link
	printf "https://raw.githubusercontent.com/LangLangBart/ImagePool/%s/storage/%s" "$(git rev-parse HEAD)" "$FILENAME" | pbcopy
	echo "File uploaded, check clipboard!"
	cd ~- || return 1
}
```
