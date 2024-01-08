
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
	local path_dir image_absolute_path filename
	path_dir="$HOME"/Developer/ImagePool
	if [ ! -d "$path_dir" ]; then
		echo "!!! The '$path_dir' directory doesn't exist."
		return 1
	fi
	if [ ${#@} -lt 1 ]; then
		echo "!!! Respect the syntax: $0 File.{png,jpg,jpeg,gif}"
		return 1
	fi
	for file in "$@"; do
		if [[ ! ${file:e} =~ ^(png|jpe?g|gif)$ ]]; then
			echo "!!! Invalid extension: ${file:t}"
			continue
		fi
		# https://zsh.sourceforge.io/Doc/Release/Expansion.html#Modifiers
		image_absolute_path="${file:A}"
		filename="$(tr -d '[:space:]' <<<"$(date +"%Y-%m-%d_%H-%M-%S")"_"${image_absolute_path:t}")"
		cp "$image_absolute_path" "$path_dir"/storage/"$filename" || continue
		git -C "$path_dir" add storage/"$filename"
		git -C "$path_dir" commit --quiet --message "$filename"
		git -C "$path_dir" push --quiet || continue
		printf "\nUploaded: %s\n" "$filename"
		printf "https://raw.githubusercontent.com/%s/%s/%s/storage/%s" "$(git config user.name)" \
			"$(basename "$(git -C "$path_dir" rev-parse --show-toplevel)")" \
			"$(git -C "$path_dir" rev-parse HEAD)" "$filename" | pbcopy
		# HINT: pbcopy/ pbpaste macOS only, use xclip on linux
		pbpaste
	done
}
```
