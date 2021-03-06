.. _migrate_images:

=======================================
Migrate images to/from KVM / vCenter DS
=======================================

Overview
--------------------------------------------------------------------------------

OpenNebula allows the management of hybrid environments, offering end-users a self-service portal to consume resources from both VMware-based infrastructures and KVM based ones to the user in a transparent way.

We are going to show you both transition below step-by-step trough Sunstone:

VMDK Image to QCOW2 Datastore
--------------------------------------------------------------------------------

Here is how it works. We have a vmdk image within MarketPlace and we want to use it in KVM:

1. Go to MarketPlace and select the image, then click on **Download Button**.

.. image:: /images/market_migrate_vmdk_qcow2_1.png
    :width: 90%
    :align: center

.. warning::

    When the destination image datastore is qcow2/raw you **must** define the attribute ``DRIVER=qcow2`` or ``DRIVER=raw``, respectively, in order to convert the image. If not, the image will be download without any change.

2. Select the destination datastore.

.. image:: /images/market_migrate_vmdk_qcow2_2.png
    :width: 90%
    :align: center

3. Create a template in order to use the new image.

.. image:: /images/market_migrate_vmdk_qcow2_3.png
    :width: 90%
    :align: center

4. Instantiate the template and check that it works.

.. image:: /images/market_migrate_vmdk_qcow2_4.png
    :width: 90%
    :align: center

QCOW2 Image to VMDK Datastore
--------------------------------------------------------------------------------

The process is very similar to the one described above.

1. Go to MarketPlace and select the image in qcow2 format to be used in a vCenter cluster, then click on **Download Button**.

.. image:: /images/market_migrate_qcow2_vmdk_1.png
    :width: 90%
    :align: center

.. note::

    In this case, when you import a vcenter datastore is automatically set ``DRIVER=vcenter`` so we dont need to define **DRIVER** attribute.

2. Select the destination image datastore.

.. image:: /images/market_migrate_qcow2_vmdk_2.png
    :width: 90%
    :align: center

3. When we download a vmdk image from the marketplace, a template is automatically created along with the image. However, we need a template with a valid vcenter ref for your cloud. You need to define an empty template in vcenter and import it in OpenNebula.

.. image:: /images/market_migrate_qcow2_vmdk_3.png
    :width: 90%
    :align: center

4. Now, clone the empty template to make use of the downloaded image.

.. image:: /images/market_migrate_qcow2_vmdk_4.png
    :width: 90%
    :align: center

5. Attach the image to the cloned template, so we can keep the original for other VMs.

.. image:: /images/market_migrate_qcow2_vmdk_5.png
    :width: 90%
    :align: center

6. Finally, instantiate the template.

.. image:: /images/market_migrate_qcow2_vmdk_6.png
    :width: 90%
    :align: center

.. image:: /images/market_migrate_qcow2_vmdk_7.png
    :width: 90%
    :align: center

In vCenter:

.. image:: /images/market_migrate_qcow2_vmdk_8.png
    :width: 90%
    :align: center

How it was implemented
--------------------------------------------------------------------------------

Everytime the image that we selected from MarketPlace is downloaded to the frontend. Then, when the download process is finished, it is converted with the ``qemu-img convert`` tool as follows:

.. prompt:: bash $ auto

    qemu-img convert -f <original_type> -O <destination_type> <original_file> <destination_file>

Then, the file is sent to the destination datastore.

Limitations and restrictions
--------------------------------------------------------------------------------

We have to take into account that when we convert an image from qcow2/raw to vmdk, the contextualization is lost so, we will have to install **VMWare tools** manually.
