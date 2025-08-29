---
title: "Replicating SQLite with Filestream"
description: "The documentation for replicaing and backing up SQLite files with Filestream."
pubDate: "2025-08-08"
heroImage: "/post_img.webp"
tags: ["SQLite"]
---


# Installation

Litestream is installed on Debian using the following command.
```
wget https://github.com/benbjohnson/litestream/releases/download/v0.3.13/litestream-v0.3.13-linux-amd64.deb
```

Then install using dpkg
`sudo dpkg -i litestream-v0.3.13-linux-amd64.deb`

Enable and start the systemd service

`sudo systemctl enable --now litestream`

If you make changes to Litestream configuration file, you’ll need to restart the service:

`sudo systemctl restart litestream`

