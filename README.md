
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
	PATH_DIR="$HOME"/Developer/ImagePool
	if [ ${#@} -lt 1 ]; then
		echo -e "!!! Respect the syntax: $0 File.{png,jpg,jpeg,gif}"
		return 1
	fi
	for i in "$@"; do
		if [[ ! ${i##*.} =~ ^(png|jpe?g|gif)$ ]]; then
			echo -e "!!! Invalid extension: ${i##*/}"
			continue
		fi
		# https://github.com/junegunn/fzf-git.sh/issues/8#issuecomment-1229345117
		IMAGE_PATH="$(ruby --disable-gems -e 'puts File.expand_path(ARGV.first)' "$i")"
		FILENAME="$(tr -d '[:space:]' <<<"$(date +"%Y-%m-%d_%H-%M-%S")"_"${IMAGE_PATH##*/}")"
		cp "$IMAGE_PATH" "$PATH_DIR"/storage/"$FILENAME" || continue
		git -C "$PATH_DIR" add storage/"$FILENAME"
		git -C "$PATH_DIR" commit --quiet --message "$FILENAME"
		git -C "$PATH_DIR" push --quiet || continue
		printf "\nUploaded: %s\n" "$FILENAME"
		printf "https://raw.githubusercontent.com/%s/%s/%s/storage/%s" "$(git config user.name)" "$(basename "$(git -C "$PATH_DIR" rev-parse --show-toplevel)")" "$(git -C "$PATH_DIR" rev-parse HEAD)" "$FILENAME" | pbcopy
		# HINT: pbcopy/ pbpaste macOS only, use xclip on linux
		pbpaste
	done
}
```
