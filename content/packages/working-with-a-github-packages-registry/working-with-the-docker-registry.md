---
title: Working with the Docker Registry
intro: '{% ifversion fpt or ghec %}The Docker registry has now been replaced by the {% data variables.product.prodname_container_registry %}.{% else %}You can push and pull your Docker images using the {% data variables.product.prodname_registry %} Docker registry.{% endif %}'
product: '{% data reusables.gated-features.packages %}'
redirect_from:
  - /articles/configuring-docker-for-use-with-github-package-registry
  - /github/managing-packages-with-github-package-registry/configuring-docker-for-use-with-github-package-registry
  - /github/managing-packages-with-github-packages/configuring-docker-for-use-with-github-packages
  - /packages/using-github-packages-with-your-projects-ecosystem/configuring-docker-for-use-with-github-packages
  - /packages/guides/container-guides-for-github-packages/configuring-docker-for-use-with-github-packages
  - /packages/guides/configuring-docker-for-use-with-github-packages
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
shortTitle: Docker Registry
---

{% ifversion fpt or ghec %}
<!-- Main versioning block for {% data variables.product.prodname_dotcom %} and GitHub Enterprise Cloud -->
{% data variables.product.prodname_dotcom %}'s Docker Registry (formerly `docker.pkg.github.com`) has been replaced by the {% data variables.product.prodname_container_registry %} (using the namespace `https://ghcr.io`). The {% data variables.product.prodname_container_registry %} offers benefits like granular permissions and storage optimizations for Docker images.

Your Docker images previously stored in the Docker Registry are automatically migrated into the {% data variables.product.prodname_container_registry %}. For more details, see "[Migrating to the Container Registry from the Docker Registry](/packages/working-with-a-github-packages-registry/migrating-to-the-container-registry-from-the-docker-registry)" and "[Working with the Container Registry](/packages/working-with-a-github-packages-registry/working-with-the-container-registry)."
{% else %}
<!-- This section is for GitHub Enterprise Server and GitHub AE -->
{% data reusables.package_registry.packages-ghes-release-stage %}
{% data reusables.package_registry.packages-ghae-release-stage %}

{% data reusables.package_registry.admins-can-configure-package-types %}

## About Docker Support

Please note that the Docker Registry does not currently support foreign layers, such as Windows images.

### Authenticating to {% data variables.product.prodname_registry %}

{% data reusables.package_registry.authenticate-packages %}

{% data reusables.package_registry.authenticate-packages-github-token %}

#### Authenticating with a {% data variables.product.pat_generic %}

{% data reusables.package_registry.required-scopes %}

You can authenticate to {% data variables.product.prodname_registry %} with Docker using the `docker login` command. To enhance security, it's recommended to save your {% data variables.product.pat_generic %} in a local file on your computer and use Docker's `--password-stdin` flag, which reads your token from a local file.

{% ifversion fpt or ghec %}
```shell
cat ~/TOKEN.txt | docker login https://docker.pkg.github.com -u USERNAME --password-stdin
```
{% endif %}

{% ifversion ghes or ghae %}
{% ifversion ghes %}
If your instance has subdomain isolation enabled:
{% endif %}
```shell
cat ~/TOKEN.txt | docker login docker.HOSTNAME -u USERNAME --password-stdin
```
{% ifversion ghes %}
If your instance has subdomain isolation disabled:
{% endif %}
```shell
cat ~/TOKEN.txt | docker login HOSTNAME -u USERNAME --password-stdin
```
{% endif %}
{% endif %}

To use this example login command, replace `USERNAME` with your {% data variables.product.product_name %} username{% ifversion ghes or ghae %}, `HOSTNAME` with the URL for {% data variables.location.product_location %},{% endif %} and `~/TOKEN.txt` with the file path to your {% data variables.product.pat_generic %} for {% data variables.product.product_name %}.

For more information, see "[Docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)."

## Publishing an Image

{% data reusables.package_registry.docker_registry_deprecation_status %}

{% note %}
**Note:** Image names must only use lowercase letters.
{% endnote %}

{% data variables.product.prodname_registry %} supports multiple top-level Docker images per repository. A repository can have any number of image tags. For Docker images larger than 10GB, layers are capped at 5GB each. For more information, see "[Docker tag](https://docs.docker.com/engine/reference/commandline/tag/)" in the Docker documentation.

{% data reusables.package_registry.viewing-packages %}

1. Determine the image name and ID for your Docker image using `docker images`.

   ```shell
   $ docker images
   > REPOSITORY        TAG        IMAGE ID       CREATED      SIZE
   > IMAGE_NAME        VERSION    IMAGE_ID       4 weeks ago  1.11MB
   ```

2. Using the Docker image ID, tag the Docker image:

   ```shell
   docker tag IMAGE_ID docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION
   ```

   If your instance has subdomain isolation enabled:

   ```shell
   docker tag IMAGE_ID docker.HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION
   ```

   If your instance has subdomain isolation disabled:

   ```shell
   docker tag IMAGE_ID HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION
   ```

3. If you haven't already built a Docker image for the package, build the image:

   ```shell
   docker build -t docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION PATH
   ```

   If your instance has subdomain isolation enabled:

   ```shell
   docker build -t docker.HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION PATH
   ```

   If your instance has subdomain isolation disabled:

   ```shell
   docker build -t HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION PATH
   ```

4. Publish the image to {% data variables.product.prodname_registry %}:

   ```shell
   docker push docker.pkg.github.com/OWNER/REPOSITORY/IMAGE_NAME:VERSION
   ```

   If your instance has subdomain isolation enabled:

   ```shell
   docker push docker.HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION
   ```

   If your instance has subdomain isolation disabled:

   ```shell
   docker push HOSTNAME/OWNER/REPOSITORY/IMAGE_NAME:VERSION
   ```

{% note %}
**Note:** You must push your image using `IMAGE_NAME:VERSION` and not using `IMAGE_NAME:SHA`.
{% endnote %}

### Example Publishing a Docker Image

{% ifversion ghes %}
These examples assume your instance has subdomain isolation enabled.
{% endif %}

