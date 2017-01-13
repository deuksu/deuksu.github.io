apply plugin: 'java'
apply plugin: 'eclipse-wtp' 
apply plugin: 'war'
 
sourceCompatibility = 1.8   // 자바 버전입니다.
targetCompatibility = 1.8
 
version = '1.0'
 
//def::로컬영역, ext::프로젝트 전체
def version = [
spring: '4.1.6.RELEASE',
servletAPI: '3.1.0',
commonsCollections: '3.2.1',
junit: '4.12',
slf4j: '1.7.12'
]
 
//프로젝트 전체와 서브 프로젝트에서도 접근 가능한 변수선언
//ext {
//	appName = 'exam-noxml'
//	compatibilityVersion = 1.8
//}

eclipse {
    project.natures "org.springsource.ide.eclipse.gradle.core.nature"
    wtp {
        component {
            contextPath = '/'
        }  
        facet {
            facet name: 'jst.web', version: '3.1'
            facet name: 'jst.java', version: '1.8'
        }
    }
}
 
jar {
    manifest {
        attributes 'Implementation-Title': 'Gradle Quickstart',
                   'Implementation-Version': version
    }
}
 
repositories {
    mavenCentral()
}
 
dependencies {
    // ${version.~}변수는 위에서 정의한 버전으로 사용됩니다.
    providedCompile "javax.servlet:javax.servlet-api:${version.servletAPI}"
     
    compile "org.springframework:spring-webmvc:${version.spring}"
     
    compile "org.slf4j:slf4j-api:${version.slf4j}"
    compile group: 'commons-collections', name: 'commons-collections', version: "${version.commonsCollections}"
    testCompile group: 'junit', name: 'junit', version: "${version.junit}"
}
 
[compileJava, compileTestJava]*.options*.encoding = 'UTF-8' // 인코딩 UTF-8로 통일합니다.
 
test {
    systemProperties 'property': 'value'
}
 
uploadArchives {
    repositories {
       flatDir {
           dirs 'repos'
       }
    }
}
