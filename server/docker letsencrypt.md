# docker let's encrypt

    docker compose -f /wf-data/docker-compose-env.yml run --rm  certbot certonly --webroot --webroot-path /usr/share/nginx/html/sakura/ -d sakura.wisefocus.net
