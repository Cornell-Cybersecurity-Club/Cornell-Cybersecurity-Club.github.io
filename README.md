# README

This is the blog for the Cornell Cybersecurity Club where we will post resources, writeups, and club updates.

## Local Setup

1. Install prerequisites described [at the official Jekyll documentation](https://jekyllrb.com/docs/installation/)
2. Clone and cd to this repository
3. Run `bundle install`
4. You should now be able to host the site locally with `bundle exec jekyll serve`

## Adding Posts

If it's your first time making a post, please contact someone on E-board for approval and guidance.

The process for adding a post should be like any other contribution to a GitHub repository. Create an issue if one does not already exist and branch for the post.

You can add posts via a Markdown file in the `_posts/` directory. Make sure to follow the correct format.

- [ ] The post name is in the format YYYY-MM-DD-name-here
- [ ] The frontmatter is properly configured (the stuff at the top between the hyphens)
- [ ] Do not use an h1 (#) heading for the title as that is added automatically

When ready, submit for review. When merged, GitHub Actions will automatically compile and deploy.

## Other Tips

- You can add icons by searching for the icon UTF code you want to use [at Nerd Fonts](https://www.nerdfonts.com/cheat-sheet) then add `&#x` in front. While not necessary, we prefer if you put icons in a span and give it the class name `icon`. For example, to show a person icon, just use `<span class="icon">&#xf415</span>`
