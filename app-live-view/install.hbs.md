# Install Application Live View

This topic describes how to install Application Live View from the Tanzu Application Platform package repository.

Application Live View installs three packages for `view`, `run`, and `build` profiles:

- For the `view` profile, Application Live View installs Application Live View back-end package (`backend.appliveview.tanzu.vmware.com`). This installs the Application Live View back-end component with Tanzu Application Platform GUI in `app-live-view` namespace.

- For the `run` profile, Application Live View installs Application Live View connector package (`connector.appliveview.tanzu.vmware.com`). This installs the Application Live View connector component as DaemonSet in `app-live-view-connector` namespace.

- For the `build` profile, Application Live View installs Application Live View Conventions package (`conventions.appliveview.tanzu.vmware.com`). This installs the Application Live View Convention Service in `app-live-view-conventions` namespace.

- For the `iterate` profile, Application Live View installs Application Live View connector package and Application Live View Conventions package.

- For the `full` profile, Application Live View installs the Application Live View back-end package, Application Live View connector package, and Application Live View Conventions package.


>**Note:** Use the instructions on this page if you do not want to use a profile to install packages.
For more information about profiles, see [About Tanzu Application Platform components and profiles](../about-package-profiles.md).


## <a id='prereqs'></a>Prerequisites

Before installing Application Live View, complete all prerequisites to install Tanzu Application Platform.
For more information, see [Prerequisites](../prerequisites.md).

In addition, install Cartographer Conventions, which is bundled with Supply Chain Choreographer as of the v0.5.3 release. To install, see [Installing Supply Chain Choreographer](../scc/install-scc.md). For more information, see [Cartographer Conventions](../cartographer-conventions/about.md).

## <a id='install-app-live-view'></a> Install Application Live View

You can install Application Live View in single cluster or multicluster environment:

- `Single cluster`: All Application Live View components are deployed in a single cluster. The user can access Application Live View plug-in information of the applications across all the namespaces in the Kubernetes cluster. This is the default mode of Application Live View.

- `Multicluster`: In a multicluster environment, the Application Live View back-end component is installed only once in a single cluster and exposes a RSocket registration for the other clusters using Tanzu shared ingress. Each cluster continues to install the connector as a DaemonSet. The connectors are configured to connect to the central instance of the Application Live View back end.


## <a id='install-app-live-view-back-end'></a> Install Application Live View back end

To install Application Live View back end:

1. List version information for the package by running:

    ```console
    tanzu package available list backend.appliveview.tanzu.vmware.com --namespace tap-install
    ```

    For example:

    ```console
    $ tanzu package available list backend.appliveview.tanzu.vmware.com --namespace tap-install
    - Retrieving package versions for backend.appliveview.tanzu.vmware.com...
      NAME                                  VERSION        RELEASED-AT
      backend.appliveview.tanzu.vmware.com  1.3.0          2022-09-07T00:00:00Z
    ```

1. (Optional) Change the default installation settings by running:

    ```console
    tanzu package available get backend.appliveview.tanzu.vmware.com/VERSION-NUMBER --values-schema --namespace tap-install
    ```

    Where `VERSION-NUMBER` is the version of the package listed. For example, `1.3.0`.

    For example:

    ```console
    $ tanzu package available get backend.appliveview.tanzu.vmware.com/1.3.0 --values-schema --namespace tap-install
      KEY                DEFAULT          TYPE     DESCRIPTION
      ingressDomain      tap.example.com  string   Domain to be used by the HTTPProxy ingress object. The "appliveview"
                                                   subdomain is prepended to the value provided. For example:
                                                   "example.com" becomes "appliveview.example.com".
      ingressEnabled     false            boolean  Flag for whether to create an HTTPProxy for ingress.

      kubernetes_flavor                   string   Kubernetes flavor

      tls.namespace      <nil>            string   The targeted namespace for secret consumption by the HTTPProxy.

      tls.secretName     <nil>            string   The name of secret for consumption by the HTTPProxy.
    ```

    For more information about values schema options, see the properties listed earlier.

2. Create `app-live-view-backend-values.yaml` with the following details:

    For a SINGLE-CLUSTER environment, the Application Live View back end is exposed through the Kubernetes cluster service.
    By default, ingress is disabled for back end.

    ```yaml
    ingressEnabled: false
    ```

    For a multicluster environment, set the flag `ingressEnabled` to true for the Application Live View back end to be exposed on the ingress domain.

     ```yaml
    backend:
        ingressEnabled: true
    ```

    >**Note:** If it is a Tanzu Application Platform profile installation and top-level key `shared.ingress_domain` is set in the `tap-values.yml`, the back end is automatically exposed through the shared ingress.

    If you want to override the shared ingress for Application Live View in a multicluster environment, use the following values:

    ```yaml
    ingressEnabled: true
    ingressDomain: ${INGRESS-DOMAIN}
    ```

    Where `INGRESS-DOMAIN` is the top-level domain you use for the `tanzu-shared-ingress` service’s
    external IP address. The `appliveview` subdomain is prepended to the value provided.

    To configure TLS certificate delegation information for the domain, add the following values to
    `app-live-view-backend-values.yaml`:

    ```yaml
    tls:
        namespace: "NAMESPACE"
        secretName: "SECRET NAME"
    ```

    Where:

    - `NAMESPACE` is the targeted namespace of TLS secret for the domain.
    - `SECRET NAME` is the name of TLS secret for the domain.

    You can edit the values to suit your project needs or leave the default values as is.

    >**Note:** The app-live-view namespace and the TLS secret for the domain should be created before installing the Tanzu Application Platform packages in the cluster so that the HTTPProxy is updated with the TLS secret. To create a TLS secret, run:

    ```console
    kubectl create -n app-live-view secret tls alv-cert --cert=<.crt file> --key=<.key file>
    ```

    To verify the HTTPProxy object with the TLS secret, run:

    ```console
    kubectl get httpproxy -A
    NAMESPACE            NAME                                                              FQDN                                                             TLS SECRET               STATUS   STATUS DESCRIPTION
    app-live-view        appliveview                                                       appliveview.192.168.42.55.nip.io                                 app-live-view/alv-cert   valid    Valid HTTPProxy
    ```


3. Install the Application Live View back-end package by running:

    ```console
    tanzu package install appliveview -p backend.appliveview.tanzu.vmware.com -v VERSION-NUMBER -n tap-install -f app-live-view-backend-values.yaml
    ```

    Where `VERSION-NUMBER` is the version of the package listed.

    For example:

    ```console
    $ tanzu package install appliveview -p backend.appliveview.tanzu.vmware.com -v 1.3.0 -n tap-install -f app-live-view-backend-values.yaml
    - Installing package 'backend.appliveview.tanzu.vmware.com'
    | Getting namespace 'tap-install'
    | Getting package metadata for 'backend.appliveview.tanzu.vmware.com'
    | Creating service account 'appliveview-tap-install-sa'
    | Creating cluster admin role 'appliveview-tap-install-cluster-role'
    | Creating cluster role binding 'appliveview-tap-install-cluster-rolebinding'
    | Creating package resource
    | Package install status: Reconciling

    Added installed package 'appliveview' in namespace 'tap-install'
    ```

    >**Note:** The Application Live View back-end component is deployed in `app-live-view` namespace by default.

4. Verify the Application Live View back-end package installation by running:

    ```console
    tanzu package installed get appliveview -n tap-install
    ```

    For example:

    ```console
    tanzu package installed get appliveview -n tap-install
    \ Retrieving installation details for appliveview...
    NAME:                    appliveview
    PACKAGE-NAME:            backend.appliveview.tanzu.vmware.com
    PACKAGE-VERSION:         1.3.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```

    Verify that `STATUS` is `Reconcile succeeded`.

## <a id='install-alv-connector'></a> Install Application Live View connector

To install Application Live View connector:

1. List version information for the package by running:

    ```console
    tanzu package available list connector.appliveview.tanzu.vmware.com --namespace tap-install
    ```

    For example:

    ```console
    $ tanzu package available list connector.appliveview.tanzu.vmware.com --namespace tap-install
    - Retrieving package versions for connector.appliveview.tanzu.vmware.com...
      NAME                                    VERSION        RELEASED-AT
      connector.appliveview.tanzu.vmware.com  1.3.0          2022-09-07T00:00:00Z
    ```

1. (Optional) Change the default installation settings by running:

    ```console
    tanzu package available get connector.appliveview.tanzu.vmware.com/VERSION-NUMBER --values-schema --namespace tap-install
    ```

    Where `VERSION-NUMBER` is the version of the package listed. For example, `1.3.0`.

    For example:

    ```console
    $ tanzu package available get connector.appliveview.tanzu.vmware.com/1.3.0 --values-schema --namespace tap-install
      KEY                     DEFAULT             TYPE     DESCRIPTION
      backend.host            <nil>               string   Domain to be used to reach the Application Live View backend. Prepend "appliveview"
                                                           subdomain to the value if you are using shared ingress. For example: "example.com"
                                                           becomes "appliveview.example.com".
      backend.ingressEnabled  false               boolean  Flag for the connector to connect to ingress on backend.

      backend.port            <nil>               number   Port to reach the Application Live View backend.
      backend.sslDisabled     false               boolean  Flag for whether to disable SSL.
      backend.caCertData      cert-in-pem-format  string   CA Cert Data for ingress domain.
      kubernetes_flavor                           string   Kubernetes flavor.
    ```

    For more information about values schema options, see the properties listed earlier.

2. Create `app-live-view-connector-values.yaml` with the following details:

    For SINGLE-CLUSTER environment, the Application Live View connector connects to the `cluster-local` Application Live View back end to register the applications.

    >**Note:** By default, ingress is disabled for connector.



    For a multicluster environment, set the flag `ingressEnabled` to true for the Application Live View connector to connect to the Application Live View back end by using the ingress domain.

     ```yaml
    backend:
        ingressEnabled: true
    ```

    >**Note:** If it is a Tanzu Application Platform profile installation and top-level key `shared.ingress_domain` is set in the `tap-values.yml`, the Application Live View connector and Application Live View back end are configured to communicate through ingress. Then the Application Live View connector uses the `shared.ingress_domain` to reach the back end.


    If you want to override the shared ingress for Application Live View in a multicluster environment, use the following values:

    ```yaml
    backend:
        host: appliveview.INGRESS-DOMAIN
    ```

    Where `INGRESS-DOMAIN` is the top level domain the Application Live View back end exposes by using `tanzu-shared-ingress` for the connectors in other clusters to reach the Application Live View back end. Prepend the `appliveview` subdomain to the provided value.

    The `backend.sslDisabled` is set to `false` by default. The CA Cert for the ingress domain can be set in the `backend.caCertData` key for ssl validation. Below is a sample yaml:

    ```yaml
    backend:
      caCertData: |-
        -----BEGIN CERTIFICATE-----
        MIIGMzCCBBugAwIBAgIJALHHzQjxM6wMMA0GCSqGSIb3DQEBDQUAMGcxCzAJBgNV
        BAgMAk1OMRQwEgYDVQQHDAtNaW5uZWFwb2xpczEPMA0GA1UECgwGVk13YXJlMRMw
        -----END CERTIFICATE-----
    ```

    If TLS is not enabled for the `INGRESS-DOMAIN` in the Application Live View back end, set the `backend.sslDisabled` to `true`.

     ```yaml
    backend:
        sslDisabled: true
    ```

    You can edit the values to suit your project needs or leave the default values as is.

    >**Note:** Using the HTTP proxy either on 80 or 443 based on SSL config exposes the back-end service running on port 7000. The connector connects to the back end on port 80/443 by default. Therefore, you are not required to explicitly configure the `port` field.


3. Install the Application Live View connector package by running:

    ```console
    tanzu package install appliveview-connector -p connector.appliveview.tanzu.vmware.com -v VERSION-NUMBER -n tap-install -f app-live-view-connector-values.yaml
    ```

    Where `VERSION-NUMBER` is the version of the package listed. For example, `1.3.0`.

    For example:

    ```console
    $ tanzu package install appliveview-connector -p connector.appliveview.tanzu.vmware.com -v 1.3.0 -n tap-install -f app-live-view-connector-values.yaml
    | Installing package 'connector.appliveview.tanzu.vmware.com'
    | Getting namespace 'tap-install'
    | Getting package metadata for 'connector.appliveview.tanzu.vmware.com'
    | Creating service account 'appliveview-connector-tap-install-sa'
    | Creating cluster admin role 'appliveview-connector-tap-install-cluster-role'
    | Creating cluster role binding 'appliveview-connector-tap-install-cluster-rolebinding'
    - Creating package resource
    / Package install status: Reconciling

    Added installed package 'appliveview-connector' in namespace 'tap-install'
    ```

    >**Note:** Each cluster installs the connector as a DaemonSet. The connector is configured to connect to the central instance of the back end. The Application Live View connector component is deployed in `app-live-view-connector` namespace by default.

4. Verify the `Application Live View connector` package installation by running:

    ```console
    tanzu package installed get appliveview-connector -n tap-install
    ```

    For example:

    ```console
    tanzu package installed get appliveview-connector -n tap-install                                                                              5s
    | Retrieving installation details for appliveview-connector...
    NAME:                    appliveview-connector
    PACKAGE-NAME:            connector.appliveview.tanzu.vmware.com
    PACKAGE-VERSION:         1.3.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```

    Verify that `STATUS` is `Reconcile succeeded`.

## <a id='install-alv-conventions'></a> Install Application Live View Conventions

To install Application Live View Conventions:

1. List version information for the package by running:

    ```console
    tanzu package available list conventions.appliveview.tanzu.vmware.com --namespace tap-install
    ```

    For example:

    ```console
    $ tanzu package available list conventions.appliveview.tanzu.vmware.com --namespace tap-install
    - Retrieving package versions for conventions.appliveview.tanzu.vmware.com...
      NAME                                      VERSION        RELEASED-AT
      conventions.appliveview.tanzu.vmware.com  1.3.0          2022-09-07T00:00:00Z
    ```

1. (Optional) Change the default installation settings by running:

    ```console
    tanzu package available get conventions.appliveview.tanzu.vmware.com/VERSION-NUMBER --values-schema --namespace tap-install
    ```

    Where `VERSION-NUMBER` is the version of the package listed. For example, `1.3.0`.

    For example:

    ```console
    $ tanzu package available get conventions.appliveview.tanzu.vmware.com/1.3.0 --values-schema --namespace tap-install
      KEY                     DEFAULT             TYPE     DESCRIPTION
      kubernetes_flavor                           string   Kubernetes flavor
    ```

    For more information about values schema options, see the properties listed earlier.

1. Install the Application Live View Conventions package by running:

    ```console
    tanzu package install appliveview-conventions -p conventions.appliveview.tanzu.vmware.com -v VERSION-NUMBER -n tap-install
    ```

    Where `VERSION-NUMBER` is the version of the package listed. For example, `1.3.0`.

    For example:

    ```console
    $ tanzu package install appliveview-conventions -p conventions.appliveview.tanzu.vmware.com -v 1.3.0 -n tap-install
    - Installing package 'conventions.appliveview.tanzu.vmware.com'
    | Getting namespace 'tap-install'
    | Getting package metadata for 'conventions.appliveview.tanzu.vmware.com'
    | Creating service account 'appliveview-conventions-tap-install-sa'
    | Creating cluster admin role 'appliveview-conventions-tap-install-cluster-role'
    | Creating cluster role binding 'appliveview-conventions-tap-install-cluster-rolebinding'
    - Creating package resource
    \ Package install status: Reconciling

    Added installed package 'appliveview-conventions' in namespace 'tap-install'
    ```

1. Verify the package install for Application Live View Conventions package by running:

    ```console
    tanzu package installed get appliveview-conventions -n tap-install
    ```

    For example:

    ```console
    tanzu package installed get appliveview-conventions -n tap-install
    | Retrieving installation details for appliveview-conventions...
    NAME:                    appliveview-conventions
    PACKAGE-NAME:            conventions.appliveview.tanzu.vmware.com
    PACKAGE-VERSION:         1.3.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:

    ```

    Verify that `STATUS` is `Reconcile succeeded`.


The Application Live View UI plug-in is part of Tanzu Application Platform GUI.
To access the Application Live View UI,
see [Application Live View in Tanzu Application Platform GUI](../tap-gui/plugins/app-live-view.md).
