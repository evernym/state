#!groovy

@Library('SovrinHelpers') _

def name = 'state-trie'

def testUbuntu = {
    try {
        echo 'Ubuntu Test: Checkout csm'
        checkout scm

        echo 'Ubuntu Test: Build docker image'
        def testEnv = dockerHelpers.build(name)

        testEnv.inside {
            echo 'Ubuntu Test: Install dependencies'
            testHelpers.installDeps()

            echo 'Ubuntu Test: Test'
            testHelpers.testJunit()
        }
    }
    finally {
        echo 'Ubuntu Test: Cleanup'
        step([$class: 'WsCleanup'])
    }
}

def testWindows = {
    try {
        echo 'Windows Test: Checkout csm'
        checkout scm

        echo 'Windows Test: Build docker image'
        dockerHelpers.buildAndRunWindows(name, testHelpers.installDepsWindowsCommands() + testHelpers.testJunitWindowsCommands())
        junit 'test-result.xml'
    }
    finally {
        echo 'Windows Test: Cleanup'
        step([$class: 'WsCleanup'])
    }
}

def testWindowsNoDocker = {
    try {
        echo 'Windows No Docker Test: Checkout csm'
        checkout scm   

        testHelpers.createVirtualEnvAndExecute({ python, pip ->
            echo 'Windows No Docker Test: Install dependencies'
            testHelpers.installDepsBat(python, pip)
            
            echo 'Windows No Docker Test: Test'
            testHelpers.testJunitBat(python, pip)
        })
    }
    finally {
        echo 'Windows No Docker Test: Cleanup'
        step([$class: 'WsCleanup'])
    }
}

//testAndPublish(name, [ubuntu: testUbuntu, windows: testWindows, windowsNoDocker: testWindowsNoDocker])

def options = new TestAndPublishOptions()
testAndPublish(name, [ubuntu: testUbuntu], true, options)
